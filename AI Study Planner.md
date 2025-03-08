
## Documentation

[Documentation](https://linktodocumentation)

AI Study Planner Project Documentation
1. Introduction
The AI Study Planner is a command-line application designed to help students and self-learners create personalized study plans. It allows users to define their study goals, schedule tasks, set deadlines, and manage their study time efficiently. While the core code focuses on data management and a basic interactive interface, the design is intended to be extended with Artificial Intelligence (AI) capabilities for automated plan generation and optimization.

2. Project Overview
This project is implemented in C++. It provides functionalities to:

Define Courses: Specify course names for study sessions.

Manage Tasks: Create, edit, and remove tasks with descriptions, priorities, and estimated times.

Schedule Study Sessions: Allocate tasks to specific study sessions with dates.

Set Exam Deadlines: Define exam dates for various courses.

Generate Study Plans: (Basic version currently) Creates a study plan based on user input. Future versions will leverage AI.

Display Study Plans: View the organized study plan with course details, tasks, and deadlines.

3. Architecture and Design
The project is structured around the following core data structures:

Task: Represents a single study task.

description (string): Task description.

priority (int): Task priority (1-5, 5 being highest).

estimatedTime (int): Estimated time to complete the task in minutes.

StudySession: Represents a study session for a specific course on a particular day.

courseName (string): Name of the course.

tasks (vector<Task>): List of tasks for the session.

totalTime (int): Total time allocated for the session in minutes.

date (chrono::system_clock::time_point): Date of the session.

Exam: Represents an exam deadline.

courseName (string): Name of the course.

date (chrono::system_clock::time_point): Exam date.

The code is organized into modular functions for each major functionality:

Task Management: addTask, removeTask, editTask

Study Session Management: displaySessionDetails

Plan Generation: generatePlan (core function to be enhanced with AI)

Plan Display: displayPlan

Exam Management: addExam, removeExam

Utility Functions: sortTasksByPriority, getDateFromUser, getIntegerInput, getStringInput, clearInputStream

4. Functionality and Usage
4.1. Main Menu
The program presents a menu-driven interface with the following options:

Generate Study Plan: Initiates the study plan generation process. Prompts for the number of days and total study time per day, then allows the user to add tasks for each session.

View Study Plan: Displays the generated study plan in an organized table format.

Add Exam Deadline: Allows adding exam deadlines.

Remove Exam Deadline: Allows removing exam deadlines.

Exit: Terminates the program.

4.2. Generating a Study Plan
Select option 1 from the main menu.

Enter the number of days for the study plan.

Enter the total study time available per day (in minutes).

For each day:

Enter the course name.

Add tasks:

Enter task description.

Enter task priority (1-5).

Enter estimated time for the task (in minutes).

The program checks if the task time exceeds the remaining study time for the session and prevents adding the task if it does.

The user is prompted to add more tasks until the study time is fully allocated or the user chooses to stop.

4.3. Viewing the Study Plan
Select option 2 from the main menu. The program displays the study plan, including:

Course name

Date

Total time allocated for the session

List of tasks with priority and estimated time

4.4. Managing Exam Deadlines
Adding: Select option 3 to add an exam. You'll be prompted for the course name and the exam date in YYYY-MM-DD format.

Removing: Select option 4 to remove an exam. A list of exams is displayed, and you are prompted to choose the exam to remove by its number.

4.5. Exiting the Program
Select option 5 to exit the program.

5. Code Structure and Explanation
Headers:

<iostream>: For input/output operations.

<vector>: For dynamic arrays.

<string>: For string manipulation.

<algorithm>: For sorting algorithms.

<iomanip>: For formatting output.

<chrono>: For date and time functionality.

<ctime>: For legacy date and time compatibility

<limits>: For clearing the input stream.

Data Structures: Defined using struct to represent tasks, study sessions, and exams.

Functions:

Input/Output Functions: getIntegerInput, getStringInput, getDateFromUser, displaySessionDetails, displayPlan. These functions handle user input and output formatting.

Task Management Functions: addTask, removeTask, editTask. These functions manage the creation, deletion, and modification of tasks within a study session.

Exam Management Functions: addExam, removeExam. These functions manage the addition and removal of exam deadlines.

Plan Generation Function: generatePlan. This function is the core of the study plan creation. Currently, it only allows for manual task entry but is intended to be expanded with AI logic in future versions.

Utility Functions: sortTasksByPriority, clearInputStream. These provide supporting functionality.

main() Function: The main entry point of the program, which presents the menu and orchestrates the program flow.

6. AI Integration (Future Enhancements)
The most significant future enhancement is the integration of AI to automate and optimize the study plan generation. Here are some potential AI techniques:

Rule-Based System: A set of rules based on exam dates, task priorities, course difficulty, and other factors. This is the simplest approach to implement.

Optimization Algorithm: Algorithms like genetic algorithms or simulated annealing could be used to find the optimal allocation of tasks to study sessions, maximizing study efficiency. Requires defining a "fitness function" that measures the quality of a study plan.

Machine Learning (ML): A trained ML model could predict the optimal study plan based on historical data, user preferences, and course characteristics. This requires a large dataset of study plans and outcomes.

6.1. AI Implementation Strategy
Data Collection (If using ML): Gather data on study habits, course performance, and study plans.

Algorithm Selection: Choose an appropriate AI algorithm based on the project's requirements and available resources. Start with a rule-based system and then potentially explore optimization algorithms or ML.

Integration into generatePlan(): Modify the generatePlan() function to use the chosen AI algorithm.

Testing and Evaluation: Thoroughly test and evaluate the performance of the AI-powered study planner.

Iterative Refinement: Continuously refine the AI algorithm based on user feedback and performance metrics.
6.2.code 
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <iomanip>
#include <chrono>
#include <ctime>
#include <limits> // Required for numeric_limits

using namespace std;

// Data Structures
struct Task {
    string description;
    int priority;
    int estimatedTime;
};

struct StudySession {
    string courseName;
    vector<Task> tasks;
    int totalTime;
    chrono::system_clock::time_point date;
};

struct Exam {
    string courseName;
    chrono::system_clock::time_point date;
};

// Function Prototypes
void addTask(StudySession& session);
void removeTask(StudySession& session);
void editTask(StudySession& session);
void displaySessionDetails(const StudySession& session);
void generatePlan(vector<StudySession>& plan, const vector<Exam>& exams, int numDays, int totalStudyMinutes); // Added totalStudyMinutes
void displayPlan(const vector<StudySession>& plan);
void addExam(vector<Exam>& exams);
void removeExam(vector<Exam>& exams);
void sortTasksByPriority(StudySession& session);
time_t getDateFromUser(const string& prompt);
int getIntegerInput(const string& prompt); // New utility for integer input with validation
string getStringInput(const string& prompt); // New utility for string input with validation
void clearInputStream(); // Utility function to clear input stream

// Utility function to clear the input stream buffer
void clearInputStream() {
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

// Utility function to safely get integer input
int getIntegerInput(const string& prompt) {
    int value;
    cout << prompt;
    while (!(cin >> value)) {
        cout << "Invalid input. Please enter an integer: ";
        cin.clear();
        clearInputStream();
    }
    clearInputStream();
    return value;
}

// Utility function to safely get string input
string getStringInput(const string& prompt) {
    string input;
    cout << prompt;
    getline(cin, input);
    return input;
}

void addTask(StudySession& session) {
    Task newTask;
    newTask.description = getStringInput("Enter task description: ");

    newTask.priority = getIntegerInput("Enter task priority (1-5, 5 being highest): ");
    while (newTask.priority < 1 || newTask.priority > 5) {
        cout << "Invalid priority. Enter a number between 1 and 5: ";
        newTask.priority = getIntegerInput(""); // Empty prompt to reuse getIntegerInput
    }

    newTask.estimatedTime = getIntegerInput("Enter estimated time for task (in minutes): ");
    while (newTask.estimatedTime <= 0) {
        cout << "Invalid time. Enter a positive number: ";
        newTask.estimatedTime = getIntegerInput(""); // Empty prompt to reuse getIntegerInput
    }

    session.tasks.push_back(newTask);
    session.totalTime += newTask.estimatedTime;
    sortTasksByPriority(session);
    cout << "Task added successfully.\n";
}

void removeTask(StudySession& session) {
    if (session.tasks.empty()) {
        cout << "No tasks to remove.\n";
        return;
    }

    cout << "Tasks:\n";
    for (size_t i = 0; i < session.tasks.size(); ++i) {
        cout << i + 1 << ". " << session.tasks[i].description << endl;
    }

    int choice = getIntegerInput("Enter the number of the task to remove: ");

    while (choice < 1 || choice > static_cast<int>(session.tasks.size())) {
        cout << "Invalid choice. Enter a number between 1 and " << session.tasks.size() << ": ";
        choice = getIntegerInput("");
    }

    session.totalTime -= session.tasks[choice - 1].estimatedTime;
    session.tasks.erase(session.tasks.begin() + choice - 1);
    cout << "Task removed successfully.\n";
}

void editTask(StudySession& session) {
    if (session.tasks.empty()) {
        cout << "No tasks to edit.\n";
        return;
    }

    cout << "Tasks:\n";
    for (size_t i = 0; i < session.tasks.size(); ++i) {
        cout << i + 1 << ". " << session.tasks[i].description << endl;
    }

    int choice = getIntegerInput("Enter the number of the task to edit: ");

    while (choice < 1 || choice > static_cast<int>(session.tasks.size())) {
        cout << "Invalid choice. Enter a number between 1 and " << session.tasks.size() << ": ";
        choice = getIntegerInput("");
    }

    Task& taskToEdit = session.tasks[choice - 1];

    string newDescription = getStringInput("Enter new task description (or press Enter to keep current): ");
    if (!newDescription.empty()) {
        taskToEdit.description = newDescription;
    }

    string newPriorityStr = getStringInput("Enter new task priority (1-5, 5 being highest, or press Enter to keep current): ");
    if (!newPriorityStr.empty()) {
        try {
            int newPriority = stoi(newPriorityStr);
            if (newPriority >= 1 && newPriority <= 5) {
                taskToEdit.priority = newPriority;
            } else {
                cout << "Invalid priority. Keeping current value.\n";
            }
        } catch (const invalid_argument& e) {
            cout << "Invalid priority format. Keeping current value.\n";
        }
    }

    string newTimeStr = getStringInput("Enter new estimated time for task (in minutes, or press Enter to keep current): ");
    if (!newTimeStr.empty()) {
        try {
            int newTime = stoi(newTimeStr);
            if (newTime > 0) {
                session.totalTime -= taskToEdit.estimatedTime;
                taskToEdit.estimatedTime = newTime;
                session.totalTime += taskToEdit.estimatedTime;
            } else {
                cout << "Invalid time. Keeping current value.\n";
            }
        } catch (const invalid_argument& e) {
            cout << "Invalid time format. Keeping current value.\n";
        }
    }

    sortTasksByPriority(session);
    cout << "Task edited successfully.\n";
}

void displaySessionDetails(const StudySession& session) {
    cout << "Course: " << session.courseName << endl;

    time_t sessionDate = chrono::system_clock::to_time_t(session.date);
    tm* timeinfo = localtime(&sessionDate);
    char buffer[80];
    strftime(buffer, sizeof(buffer), "%Y-%m-%d", timeinfo);

    cout << "Date: " << buffer << endl;
    cout << "Total Time: " << session.totalTime << " minutes\n";

    cout << "Tasks:\n";
    if (session.tasks.empty()) {
        cout << "  No tasks scheduled.\n";
    } else {
        cout << setw(5) << "Priority" << setw(15) << "Time (min)" << " Description\n";
        for (const auto& task : session.tasks) {
            cout << setw(5) << task.priority << setw(15) << task.estimatedTime << " " << task.description << endl;
        }
    }
}

void generatePlan(vector<StudySession>& plan, const vector<Exam>& exams, int numDays, int totalStudyMinutes) {
    plan.clear();

    auto now = chrono::system_clock::now();

    for (int i = 0; i < numDays; ++i) {
        StudySession session;
        session.courseName = getStringInput("Enter course name for day " + to_string(i + 1) + ": ");

        session.date = now + chrono::hours(24 * i);
        session.totalTime = 0;
        plan.push_back(session);

        int sessionStudyMinutes = totalStudyMinutes; // Use provided daily study time

        char addMore;
        do {
            if(sessionStudyMinutes <= 0) {
                cout << "No more study time available for this session.\n";
                break; //Stop adding task for this session
            }

            addTask(plan.back());
            if (plan.back().tasks.back().estimatedTime > sessionStudyMinutes) {
                cout << "Task exceeds remaining study time for the session.  Please adjust.\n";
                plan.back().totalTime -= plan.back().tasks.back().estimatedTime; //undo the add
                plan.back().tasks.pop_back();
                continue; // back to do loop
            }

            sessionStudyMinutes -= plan.back().tasks.back().estimatedTime;  // Update available minutes
            cout << "Remaining study time for this session: " << sessionStudyMinutes << " minutes.\n";

            cout << "Add another task for this session? (y/n): ";
            cin >> addMore;
            clearInputStream(); // Consume any remaining newline.
        } while (addMore == 'y' || addMore == 'Y');
    }

    cout << "Study plan generated.\n";
}

void displayPlan(const vector<StudySession>& plan) {
    if (plan.empty()) {
        cout << "No study plan generated yet.\n";
        return;
    }

    cout << "--- Study Plan ---\n";
    for (const auto& session : plan) {
        displaySessionDetails(session);
        cout << "------------------\n";
    }
}

void addExam(vector<Exam>& exams) {
    Exam newExam;
    newExam.courseName = getStringInput("Enter course name for the exam: ");

    time_t examDate = getDateFromUser("Enter exam date (YYYY-MM-DD): ");
    newExam.date = chrono::system_clock::from_time_t(examDate);

    exams.push_back(newExam);
    cout << "Exam added.\n";
}

void removeExam(vector<Exam>& exams) {
    if (exams.empty()) {
        cout << "No exams to remove.\n";
        return;
    }

    cout << "Exams:\n";
    for (size_t i = 0; i < exams.size(); ++i) {
        time_t examDate = chrono::system_clock::to_time_t(exams[i].date);
        tm* timeinfo = localtime(&examDate);
        char buffer[80];
        strftime(buffer, sizeof(buffer), "%Y-%m-%d", timeinfo);

        cout << i + 1 << ". " << exams[i].courseName << " - " << buffer << endl;
    }

    int choice = getIntegerInput("Enter the number of the exam to remove: ");

    while (choice < 1 || choice > static_cast<int>(exams.size())) {
        cout << "Invalid choice. Enter a number between 1 and " << exams.size() << ": ";
        choice = getIntegerInput("");
    }

    exams.erase(exams.begin() + choice - 1);
    cout << "Exam removed successfully.\n";
}

void sortTasksByPriority(StudySession& session) {
    sort(session.tasks.begin(), session.tasks.end(), [](const Task& a, const Task& b) {
        return a.priority > b.priority;
    });
}

time_t getDateFromUser(const string& prompt) {
    tm timeinfo = {};
    string dateStr = getStringInput(prompt);

    strptime(dateStr.c_str(), "%Y-%m-%d", &timeinfo);
    time_t rawtime = mktime(&timeinfo);

    if (rawtime == -1) {
        cout << "Invalid date format. Please use YYYY-MM-DD.\n";
        return getDateFromUser(prompt);
    }

    return rawtime;
}

int main() {
    vector<StudySession> studyPlan;
    vector<Exam> examDeadlines;
    int numDays;
    int totalStudyMinutes; // Total study time per day
    char choice;

    do {
        cout << "\n--- Study Plan Generator Menu ---\n";
        cout << "1. Generate Study Plan\n";
        cout << "2. View Study Plan\n";
        cout << "3. Add Exam Deadline\n";
        cout << "4. Remove Exam Deadline\n";
        cout << "5. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;
        clearInputStream();  // consume newline after the choice.

        switch (choice) {
            case '1': {
                numDays = getIntegerInput("Enter the number of days for the study plan: ");
                while (numDays <= 0) {
                    cout << "Invalid number of days. Enter a positive number: ";
                    numDays = getIntegerInput("");
                }

                totalStudyMinutes = getIntegerInput("Enter the total study time per day (in minutes): ");
                while (totalStudyMinutes <= 0) {
                    cout << "Invalid study time. Enter a positive number: ";
                    totalStudyMinutes = getIntegerInput("");
                }

                generatePlan(studyPlan, examDeadlines, numDays, totalStudyMinutes);
                break;
            }
            case '2':
                displayPlan(studyPlan);
                break;
            case '3':
                addExam(examDeadlines);
                break;
            case '4':
                removeExam(examDeadlines);
                break;
            case '5':
                cout << "Exiting...\n";
                break;
            default:
                cout << "Invalid choice. Try again.\n";
        }
    } while (choice != '5');

    return 0;
}
7. Build and Execution Instructions
Compiler: A C++ compiler (e.g., g++) is required.

Compilation: Compile the code using the following command:

g++ study_planner.cpp -o study_planner
Use code with caution.
Bash
Execution: Run the compiled program using:

8. Testing
The testing strategy involves:

Unit Testing: Testing individual functions to ensure they work correctly. (Consider using a C++ testing framework like Google Test)

Integration Testing: Testing the interaction between different functions.

User Acceptance Testing: Having users test the application and provide feedback.

9. Future Enhancements
AI-Powered Plan Generation: Implement AI algorithms to automate and optimize the study plan generation process.

User Interface (UI): Develop a graphical user interface (GUI) for a more user-friendly experience. Consider using a cross-platform framework like Qt or wxWidgets.

Data Persistence: Implement data persistence to save and load study plans from files (e.g., using JSON or XML).

Integration with Calendar Applications: Allow users to sync their study plans with their calendar applications (e.g., Google Calendar, Outlook Calendar).

Progress Tracking: Add features to track study progress and adjust the plan accordingly.

Personalization: Implement more personalization options to tailor study plans to individual learning styles and preferences.

Advanced Reporting: Generate reports on study progress, time allocation, and task completion.

10. Conclusion
The AI Study Planner project provides a solid foundation for building a powerful and intelligent study planning tool. By integrating AI algorithms and adding user-friendly features, this project has the potential to significantly enhance the learning experience for students and self-learners. The most critical next step is to begin implementing the AI logic in the generatePlan() function.