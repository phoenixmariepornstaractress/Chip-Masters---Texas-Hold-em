#include <iostream>
#include <vector>
#include <algorithm>
#include <ctime>
#include <cstdlib>
#include <map>
#include <set>
#include <iomanip>

// Card structure
struct Card {
    std::string suit;
    std::string value;
};

// Player structure
struct Player {
    std::string name;
    int chips;
    std::vector<Card> hand;
    bool folded = false;
    bool allIn = false;
};

// Generate a deck of cards
std::vector<Card> generateDeck() {
    std::vector<Card> deck;
    std::vector<std::string> suits = {"Hearts", "Diamonds", "Clubs", "Spades"};
    std::vector<std::string> values = {"2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K", "A"};
    
    for (const auto& suit : suits) {
        for (const auto& value : values) {
            deck.push_back({suit, value});
        }
    }
    return deck;
}

// Shuffle the deck
void shuffleDeck(std::vector<Card>& deck) {
    std::srand(unsigned(std::time(0)));
    std::random_shuffle(deck.begin(), deck.end());
}

// Deal cards to players
std::vector<Card> dealCards(std::vector<Card>& deck, int numCards) {
    std::vector<Card> hand;
    for (int i = 0; i < numCards; ++i) {
        hand.push_back(deck.back());
        deck.pop_back();
    }
    return hand;
}

// Print cards
void printCards(const std::vector<Card>& cards) {
    for (const auto& card : cards) {
        std::cout << card.value << " of " << card.suit << "\n";
    }
}

// Hand evaluation function
int evaluateHand(const std::vector<Card>& hand, const std::vector<Card>& communityCards) {
    std::map<std::string, int> valueCount;
    std::map<std::string, int> suitCount;
    std::set<std::string> values;
    
    for (const auto& card : hand) {
        valueCount[card.value]++;
        suitCount[card.suit]++;
        values.insert(card.value);
    }
    for (const auto& card : communityCards) {
        valueCount[card.value]++;
        suitCount[card.suit]++;
        values.insert(card.value);
    }

    bool flush = std::any_of(suitCount.begin(), suitCount.end(), [](const auto& entry) { return entry.second >= 5; });
    bool straight = false;
    std::vector<std::string> order = {"2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K", "A"};
    for (size_t i = 0; i <= order.size() - 5; ++i) {
        if (values.count(order[i]) && values.count(order[i + 1]) && values.count(order[i + 2]) &&
            values.count(order[i + 3]) && values.count(order[i + 4])) {
            straight = true;
            break;
        }
    }

    if (flush && straight) return 8;
    if (std::any_of(valueCount.begin(), valueCount.end(), [](const auto& entry) { return entry.second == 4; })) return 7;
    if (std::any_of(valueCount.begin(), valueCount.end(), [](const auto& entry) { return entry.second == 3; }) &&
        std::any_of(valueCount.begin(), valueCount.end(), [](const auto& entry) { return entry.second == 2; })) return 6;
    if (flush) return 5;
    if (straight) return 4;
    if (std::any_of(valueCount.begin(), valueCount.end(), [](const auto& entry) { return entry.second == 3; })) return 3;
    if (std::count_if(valueCount.begin(), valueCount.end(), [](const auto& entry) { return entry.second == 2; }) >= 2) return 2;
    if (std::any_of(valueCount.begin(), valueCount.end(), [](const auto& entry) { return entry.second == 2; })) return 1;

    return 0;
}

// Get the best hand description
std::string getHandDescription(int handValue) {
    switch (handValue) {
        case 8: return "Straight Flush";
        case 7: return "Four of a Kind";
        case 6: return "Full House";
        case 5: return "Flush";
        case 4: return "Straight";
        case 3: return "Three of a Kind";
        case 2: return "Two Pair";
        case 1: return "One Pair";
        case 0: return "High Card";
        default: return "Unknown";
    }
}

// Player action (betting)
void playerAction(Player& player, int& pot, int& currentBet, int minBet) {
    if (player.folded || player.allIn) {
        return;
    }

    std::cout << player.name << "'s turn. Chips: " << player.chips << "\n";
    std::cout << "Current bet to call: " << currentBet << " chips\n";
    std::cout << "1. Call\n2. Raise\n3. Fold\n4. Check\n5. All-in\n";
    int action;
    std::cin >> action;

    switch (action) {
        case 1: // Call
            if (currentBet <= player.chips) {
                player.chips -= currentBet;
                pot += currentBet;
                std::cout << player.name << " calls " << currentBet << " chips.\n";
            } else {
                pot += player.chips;
                currentBet += player.chips;
                player.chips = 0;
                player.allIn = true;
                std::cout << player.name << " goes all-in with " << currentBet << " chips.\n";
            }
            break;
        case 2: // Raise
            std::cout << "Enter raise amount (min raise " << minBet << " chips): ";
            int raise;
            std::cin >> raise;
            if (raise >= minBet && raise <= player.chips) {
                player.chips -= raise;
                pot += raise;
                currentBet += raise;
                std::cout << player.name << " raises to " << currentBet << " chips.\n";
            } else {
                std::cout << "Invalid raise amount. Please choose again.\n";
                playerAction(player, pot, currentBet, minBet);
            }
            break;
        case 3: // Fold
            player.folded = true;
            std::cout << player.name << " folds.\n";
            break;
        case 4: // Check
            if (currentBet == 0) {
                std::cout << player.name << " checks.\n";
            } else {
                std::cout << "You cannot check. Please choose another action.\n";
                playerAction(player, pot, currentBet, minBet);
            }
            break;
        case 5: // All-in
            pot += player.chips;
            currentBet += player.chips;
            player.chips = 0;
            player.allIn = true;
            std::cout << player.name << " goes all-in with " << currentBet << " chips.\n";
            break;
        default:
            std::cout << "Invalid action. Please choose again.\n";
            playerAction(player, pot, currentBet, minBet);
    }
}

// Print community cards
void printCommunityCards(const std::vector<Card>& communityCards, int numCards) {
    std::cout << "\nCommunity cards:\n";
    for (int i = 0; i < numCards; ++i) {
        std::cout << communityCards[i].value << " of " << communityCards[i].suit << "\n";
    }
}

// Check if the game is over
bool isGameOver(const Player& player1, const Player& dealer) {
    return player1.chips == 0 || dealer.chips == 0;
}

// Display the winner
void displayWinner(const Player& player1, const Player& dealer) {
    if (player1.chips > dealer.chips) {
        std::cout << "\n" << player1.name << " wins the game with " << player1.chips << " chips!\n";
    } else if (dealer.chips > player1.chips) {
        std::cout << "\n" << dealer.name << " wins the game with " << dealer.chips << " chips!\n";
    } else {
        std::cout << "\nIt's a tie! Both players have " << player1.chips << " chips.\n";
    }
}

// Main game loop
int main() {
    char restart;
    do {
        Player player1 = {"Player 1", 1000, {}};
        Player dealer = {"Dealer", 1000, {}};

        int smallBlind = 10;
        int bigBlind = 20;
        bool player1SmallBlind = true;

        while (!isGameOver(player1, dealer)) {
            std::vector<Card> deck = generateDeck();
            shuffleDeck(deck);

            player1.hand = dealCards(deck, 2);
            dealer.hand = dealCards(deck, 2);
            std::vector<Card> communityCards = dealCards(deck, 5);

            std::cout << "\nPlayer's hand:\n";
            printCards(player1.hand);

            int pot = 0;
            int currentBet = 0;
            int minRaise = bigBlind;

            // Blinds
            if (player1SmallBlind) {
                player1.chips -= smallBlind;
                dealer.chips -= bigBlind;
                pot += smallBlind + bigBlind;
                currentBet = bigBlind;
            } else {
                dealer.chips -= smallBlind;
                player1.chips -= bigBlind;
                pot += smallBlind + bigBlind;
                currentBet = bigBlind;
            }

            // Pre-flop betting round
            playerAction(player1, pot, currentBet, minRaise);
            playerAction(dealer, pot, currentBet, minRaise);

            // Flop
            printCommunityCards(communityCards, 3);
            playerAction(player1, pot, currentBet, minRaise);
            playerAction(dealer, pot, currentBet, minRaise);

            // Turn
            printCommunityCards(communityCards, 4);
            playerAction(player1, pot, currentBet, minRaise);
            playerAction(dealer, pot, currentBet, minRaise);

            // River
            printCommunityCards(communityCards, 5);
            playerAction(player1, pot, currentBet, minRaise);
            playerAction(dealer, pot, currentBet, minRaise);

            // Hand evaluation
            int playerScore = evaluateHand(player1.hand, communityCards);
            int dealerScore = evaluateHand(dealer.hand, communityCards);

            std::cout << "\nPlayer's best hand: " << getHandDescription(playerScore) << "\n";
            std::cout << "Dealer's best hand: " << getHandDescription(dealerScore) << "\n";

            if (playerScore > dealerScore) {
                std::cout << "Player wins the pot of " << pot << " chips!\n";
                player1.chips += pot;
            } else if (playerScore < dealerScore) {
                std::cout << "Dealer wins the pot of " << pot << " chips!\n";
                dealer.chips += pot;
            } else {
                std::cout << "It's a tie! Pot is split.\n";
                player1.chips += pot / 2;
                dealer.chips += pot / 2;
            }

            std::cout << "\nPlayer's chips: " << player1.chips << "\n";
            std::cout << "Dealer's chips: " << dealer.chips << "\n";
            
            // Reset players for next round
            player1.folded = false;
            player1.allIn = false;
            dealer.folded = false;
            dealer.allIn = false;

            // Rotate blinds
            player1SmallBlind = !player1SmallBlind;
        }

        displayWinner(player1, dealer);

        std::cout << "\nDo you want to restart the game? (y/n): ";
        std::cin >> restart;
    } while (restart == 'y' || restart == 'Y');

    return 0;
}
