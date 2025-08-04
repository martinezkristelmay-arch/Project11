#include <iostream>
#include <string>
#include <filesystem>

using namespace std;
namespace fs = std::filesystem;

void listFiles(const fs::path& path) {
    int choice;
    string input;
    cout << "\n[1] List All Files\n[2] List Files by Extension\n[3] List Files by Pattern\nEnter choice: ";
    cin >> choice;
    cin.ignore();

    for (const auto& entry : fs::directory_iterator(path)) {
        if (!entry.is_regular_file()) continue; // Skip non-regular files
        string filename = entry.path().filename().string();

        if (choice == 1) {
            cout << filename << "\n";
        }
        else if (choice == 2) {
            cout << "Enter extension (e.g., .txt): ";
            getline(cin, input);
            if (entry.path().extension() == input) {
                cout << filename << "\n";
            }
        }
        else if (choice == 3) {
            cout << "Enter pattern prefix (e.g., test): ";
            getline(cin, input);
            if (filename.find(input) == 0) {
                cout << filename << "\n";
            }
        }
        else {
            cout << "Invalid choice.\n";
            break;
        }
    }
}

void createDirectory(fs::path& currentPath) {
    string name;
    cout << "Enter directory name: ";
    getline(cin, name);
    fs::path newDir = currentPath / name;

    if (fs::exists(newDir)) {
        cout << "Error: Already exists.\n";
    }
    else if (fs::create_directory(newDir)) {
        cout << "Directory created.\n";
    }
    else {
        cout << "Failed to create.\n";
    }
}

void changeDirectory(fs::path& currentPath) {
    string newPathStr;
    cout << "Enter new directory path: ";
    getline(cin, newPathStr);
    fs::path newPath(newPathStr);

    if (fs::exists(newPath) && fs::is_directory(newPath)) {
        currentPath = newPath;
        cout << "Changed to: " << currentPath << "\n";
    }
    else {
        cout << "Error: Directory not found.\n";
    }
}

int main() {
    fs::path currentPath = fs::current_path();

    int option;
    do {
        cout << "\nCurrent Directory: " << currentPath << "\n";
        cout << "[1] List Files\n[2] Create Directory\n[3] Change Directory\n[4] Exit\nEnter choice: ";
        cin >> option;
        cin.ignore();

        switch (option) {
        case 1:
            listFiles(currentPath);
            break;
        case 2:
            createDirectory(currentPath);
            break;
        case 3:
            changeDirectory(currentPath);
            break;
        case 4:
            cout << "Exiting...\n";
            break;
        default:
            cout << "Invalid choice.\n";
        }
    } while (option != 4);

    return 0;
}
