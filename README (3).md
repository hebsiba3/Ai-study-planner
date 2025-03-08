
## Documentation

[Documentation](https://linktodocumentation)

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
