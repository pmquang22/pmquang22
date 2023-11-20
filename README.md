#include <iostream>
#include <string>
#include <fstream>
#include <vector>

const std::string FILE_NAME = "group.txt";

struct Group {
    std::string nameOfGroup;
    std::vector<std::string> nameOfStudentList;

    struct ProjectDateSubmitOfGroup {
        std::string projectSubmitList;
        int dayOfSubmit, monthOfSubmit, yearOfSubmit;
        std::string statement;
    };

    std::vector<ProjectDateSubmitOfGroup*> projectDateSubmitOfGroupList;

    ~Group() {
        for (auto project : projectDateSubmitOfGroupList) {
            delete project;
        }
    }
};

struct HandleData {
    std::vector<Group*> listOfGroup;
// Option 1
    ~HandleData() {
        for (auto group : listOfGroup) {
            delete group;
        }
    }

    void ReadFile() {
        std::ifstream groupFile(FILE_NAME);
        if (!groupFile.is_open()) {
            std::cerr << "Error opening file: " << FILE_NAME << std::endl;
            return;
        }

        std::string groupInformation;
        Group* group = nullptr;

        while (std::getline(groupFile, groupInformation)) {
            if (groupInformation.find("Group") != std::string::npos) {
                group = new Group();
                group->nameOfGroup = groupInformation;
                listOfGroup.push_back(group);
            } else {
                if (group) {
                    group->nameOfStudentList.push_back(groupInformation);
                }
            }
        }
    }

    void InputInformation() {
        int numberOfGroupAdd = 0;

        std::cout << "(!)Class already has " << listOfGroup.size() << " groups." << "\n";
        while (numberOfGroupAdd <= 0) {
            std::cout << "(-)Enter the number of groups in the class you want to add: ";
            std::cin >> numberOfGroupAdd;

            if (numberOfGroupAdd <= 0) {
                std::cout << "(!) You need to enter the number of added groups greater than 0. Please re-enter\n";
            }
        }

        for (int i = 0; i < numberOfGroupAdd; i++) {
            Group* newGroup = new Group();
            newGroup->nameOfGroup = "Group " + std::to_string(listOfGroup.size() + 1);

            int numberOfStudentInAGroup = 0;
            std::cout << "Enter information for " << newGroup->nameOfGroup << "\n";

            while (numberOfStudentInAGroup <= 0) {
                std::cout << "(-)Enter number of students: ";
                std::cin >> numberOfStudentInAGroup;

                if (numberOfStudentInAGroup <= 0) {
                    std::cout << "(!) You need to enter the number of added students greater than 0. Please re-enter\n";
                }
            }

            std::cin.ignore();
            for (int j = 0; j < numberOfStudentInAGroup; j++) {
                std::string nameOfStudent = "";
                bool isDuplicate = false;

                do {
                    isDuplicate = false;
                    std::cout << "(-)Enter name of student number " << j + 1 << ": ";
                    std::getline(std::cin, nameOfStudent);

                    for (auto existingGroup : listOfGroup) {
                        for (auto& existingStudent : existingGroup->nameOfStudentList) {
                            if (existingStudent == nameOfStudent) {
                                isDuplicate = true;
                                std::cout << "(!)Student already exists in another group. Please re-enter\n";
                                break;
                            }
                        }

                        if (isDuplicate) {
                            break;
                        }
                    }
                } while (isDuplicate);

                newGroup->nameOfStudentList.push_back(nameOfStudent);
            }

            listOfGroup.push_back(newGroup);
        }
    }

    void SaveInformation() {
        std::ofstream groupFile(FILE_NAME);
        if (!groupFile.is_open()) {
            std::cerr << "Error opening file for writing: " << FILE_NAME << std::endl;
            return;
        }

        for (auto group : listOfGroup) {
            groupFile << group->nameOfGroup << "\n";
            for (auto& nameOfStudent : group->nameOfStudentList) {
                groupFile << nameOfStudent << "\n";
            }
        }
    }
    void DisplayData() {
        for (auto group : listOfGroup) {
            std::cout << group->nameOfGroup << ";";
            for (auto& nameOfStudent : group->nameOfStudentList) {
                std::cout << nameOfStudent << ";";
            }
            std::cout << "\n";
        }
    }
    // Option 3
    void SubmitProject() {
        int groupIndex, projectIndex;
        Group::ProjectDateSubmitOfGroup* projectDateSubmitGroup = new Group::ProjectDateSubmitOfGroup();

        std::cout << "Enter group number for project submission: ";
        std::cin >> groupIndex;

        if (groupIndex < 1 || groupIndex > listOfGroup.size()) {
            std::cout << "Invalid group number. Please try again.\n";
            delete projectDateSubmitGroup;
            return;
        }

        std::cout << "Enter project number: ";
        std::cin >> projectIndex;

        if (projectIndex < 1 || projectIndex > listOfGroup[groupIndex - 1]->projectDateSubmitOfGroupList.size() + 1) {
            std::cout << "Invalid project number. Please try again.\n";
            delete projectDateSubmitGroup;
            return;
        }

        projectDateSubmitGroup->projectSubmitList = "Project " + std::to_string(projectIndex);
        projectDateSubmitGroup->statement = "Submitted";
        std::cout << "Enter submission date (day month year): ";
        std::cin >> projectDateSubmitGroup->dayOfSubmit >> projectDateSubmitGroup->monthOfSubmit >> projectDateSubmitGroup->yearOfSubmit;

        listOfGroup[groupIndex - 1]->projectDateSubmitOfGroupList.push_back(projectDateSubmitGroup);
        std::cout << "Project submitted successfully!\n";
    }

};

int main() {
    HandleData* handleData = new HandleData();
    handleData->ReadFile();

    std::string option;
    while (true) {
        std::cout << "(1) Group Information\n";
        std::cout << "(3) Submit Project\n";
        std::cout << "Enter your option: ";
        std::cin >> option;

        if (option == "1") {
            std::string optionOf1 = "";
            std::cout << "(1) Input information of groups\n"
                      << "(2) Display information\n"
                      << "(3) Save groups information\n";

            std::cout << "Enter your option: ";
            std::cin >> optionOf1;

            if (optionOf1 == "1") {
                handleData->InputInformation();
            } else if (optionOf1 == "2") {
                handleData->DisplayData();
            } else if (optionOf1 == "3") {
                handleData->SaveInformation();
            }
        } else if (option == "3") {
            handleData->SubmitProject();
        }

        std::cout << "(*)Press enter to go back to the menu...";
        std::cin.ignore();
        std::getline(std::cin, option);
        system("cls");
    }

    delete handleData;
    return 0;
}

