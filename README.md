# Itinerary Travel Planner
#include <iostream>
#include <string>
#include <fstream>
#include <windows.h>
#include <filesystem>
#include <conio.h>
#include <sstream>

namespace fs = std::filesystem;

void welcome_intro();
void intro_question(int &response);
void create_account(std::string &username, std::string &password);
bool user_login(std::string &username, std::string &password);
void create_itinerary();
void view_itinerary();
void top_itineraries();
void reset_password();
void delete_account();

int main(){
    int choice = 0;
    std::string name;
    std::string pass;

    fs::create_directory("users_data");
    SetConsoleOutputCP(CP_UTF8); // enable emoji display

    welcome_intro();

    do {
        intro_question(choice);

        switch (choice) {
            case 1:
                create_account(name, pass);
                break;
            case 2:
                if(user_login(name, pass)){

                }
                break;
            case 3:
                std::cout << "\n🌅 Thank you for using ITINERA!\n"
                          << "✈️  Safe travels and see you next time!\n";
                break;
            default:
                std::cout << "\n❌ Invalid choice. Please enter 1, 2, or 3.\n";
                break;
        }

        if (choice != 3) {
            std::cout << "\nPress any key to return to the main menu...";
            _getch(); // pause before showing the menu again
        }

    } while (choice != 3);

    return 0;
}

void welcome_intro(){
    std::cout << "============================================================\n" 
                 "             🧭  Welcome to ITINERA  🗺️                     \n"    
                 "============================================================\n"
                 "    \"Your Personal Travel Itinerary Planner\"\n"
                 "------------------------------------------------------------\n"
                 "🌍 Plan, organize, and enjoy your trips the smart way!      \n"
                 "✈️  Explore top destinations across the Philippines.        \n"
                 "📅 Create personalized itineraries with activities per city.\n"
                 "🧳 Discover places, manage your time, and travel with ease. \n"
                 "============================================================" << std::endl;
    std::cout << "Press any key to continue...";
    _getch(); // waits for any key press
}

void intro_question(int &response){

    std::cout << "\nDo you have an account in Itinera?\n"
                 "1. Create Account.\n"
                 "2. Login.\n"
                 "3. Exit.\n"
                 "Enter you choice: ";
    std::cin >> response;

}

void create_account(std::string &username, std::string &password){
    std::string email;
    std::string sex;
    std::string birthday;

    std::cout << "🌐=============================================🌐\n"
                 "🧭 WELCOME TO ITINERA ACCOUNT CREATION 🧭\n"
                 "🌐=============================================🌐\n"
                 "📢 Please fill in your details to create an account.\n"
                 "🔐 Your information will be securely stored.\n" << std::endl;

    std::cout << "-----------------------------------------------" << std::endl;
    bool isvalid_email = false;
    do{
        std::cout << "📧 Email Address         : ";
        std::cin >> email;
        for(char e : email){
            if(e == '@'){
                isvalid_email = true;
                break;
            }
        }
        if(!isvalid_email){
            std::cout << "Invalid email, please enter your correct email." << std::endl;
        }
    } while(!isvalid_email);

    std::cout << "👤 Username              : ";
    std::cin.ignore();
    std::getline(std::cin, username);
    std::cout << "🔑 Password              : ";
    std::cin >> password;
    std::cout << "🚻 Sex (M/F)             : ";
    std::cin >> sex;
    std::cout << "🎂 Birthday (MM/DD/YYYY) : ";
    std::cin >> birthday;
    std::cout << "-----------------------------------------------" << std::endl;

    std::cout << "🔎 Checking if username already exists...\n";
                 "⏳ Please wait...\n";
    Sleep(3000);
    
    bool username_exist;
    do {
        username_exist = false;
        std::ifstream credentials("accounts.csv");
        std::string line, stored_username;

        while (std::getline(credentials, line)) {
            std::stringstream ss(line);
            std::getline(ss, stored_username, ','); // Get the first field = username

            if (username == stored_username) {
                std::cout << "\n❗ Username already exists, please choose another.\n";
                username_exist = true;

                std::cout << "👤 Username (should not include spaces): ";
                std::getline(std::cin, username);

                std::cout << "Checking your username if it matches existing accounts...\n";
                Sleep(2000);
                break; // Stop checking other lines, restart loop
            }
        }

        credentials.close();

    } while (username_exist);
  
    
    if(!username_exist){
        std::ofstream credentials("accounts.csv", std::ios::app);
        if(credentials.is_open()){
            std::cout << "✅ Account created successfully!" << std::endl;
            credentials << username << "," << password << "," << email << "," << sex << "," << birthday << "\n";
            credentials.close();
            std::cout << "📁 A new file has been generated: users_data/[" << username << "].csv\n";
            std::cout << "🏁 You are now ready to plan your adventure with ITINERA! 🌍✈️" << std::endl;
            std::string filename = username + ".csv";
            std::ofstream data("users_data/" + username + ".csv");
            data.close();
        }
    }
}

bool user_login(std::string &username, std::string &password){ 

    bool is_authenticated = false;

    std::cout << "\n🔐============================================🔐\n";
    std::cout << "           🧭 ITINERA USER LOGIN 🧭\n";
    std::cout << "🔐============================================🔐\n";
    std::cout << "Please enter your login credentials below:\n";
    std::cout << "--------------------------------------------\n";

    std::cin.ignore(); // clear input buffer
    std::cout << "👤 Username: ";
    std::getline(std::cin, username);

    std::cout << "🔑 Password: ";
    std::getline(std::cin, password);

    std::ifstream credentials("accounts.csv");
    std::string line;

    while (std::getline(credentials, line)) {
        std::stringstream ss(line);
        std::string stored_username, stored_password;

        std::getline(ss, stored_username, ',');
        std::getline(ss, stored_password, ',');

        if (username == stored_username && password == stored_password) {
            is_authenticated = true;
            break;
            
        }
    }
    
    if (is_authenticated) {
        std::cout << "\n✅ Login successful! Welcome back, " << username << " 🌍\n";
    } else {
        std::cout << "\n❌ Invalid username or password. Please try again.\n";
    }

    std::cout << "Press any key to continue...";
    _getch();

    return is_authenticated;
}

void create_itinerary(const std::string& username) {
    std::string destination, start_date, end_date, activity;
    std::ofstream user_file("users_data/" + username + ".csv", std::ios::app);

    if (!user_file.is_open()) {
        std::cout << "\n❌ Failed to open your itinerary file.\n";
        return;
    }

    char add_more;
    std::cout << "\n🧭===============================================🧭\n";
    std::cout << "          🌴 CREATE YOUR TRAVEL ITINERARY 🌴\n";
    std::cout << "🧭===============================================🧭\n";
    std::cout << "✍️  Fill in the details below to plan your trip:\n";
    std::cout << "---------------------------------------------------\n";

    do {
        std::cout << "\n📍 Destination City: ";
        std::cin.ignore();
        std::getline(std::cin, destination);

        std::cout << "📅 Start Date (MM/DD/YYYY): ";
        std::getline(std::cin, start_date);

        std::cout << "📅 End Date (MM/DD/YYYY): ";
        std::getline(std::cin, end_date);

        std::cout << "🎯 Planned Activity: ";
        std::getline(std::cin, activity);

        // Save entry
        user_file << destination << "," << start_date << "," << end_date << "," << activity << "\n";

        std::cout << "\n✅ Itinerary added successfully!\n";
        std::cout << "➕ Do you want to add another entry? (Y/N): ";
        std::cin >> add_more;

    } while (toupper(add_more) == 'Y');

    user_file.close();
    std::cout << "\n📁 Your updated itinerary has been saved!\n";
    std::cout << "🏁 Happy planning with ITINERA! ✈️🌍\n";
    std::cout << "\nPress any key to return to the main menu...";
    _getch();
}

