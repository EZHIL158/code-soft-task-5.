# code-soft-task-5.
import java.io.*;
import java.util.*;

public class StudentManagementSystem {

    // Inner class to represent a Student
    static class Student implements Serializable {
        private String name;
        private int rollNumber;
        private String grade;

        // Constructor
        public Student(String name, int rollNumber, String grade) {
            this.name = name;
            this.rollNumber = rollNumber;
            this.grade = grade;
        }

        // Getters and Setters
        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public int getRollNumber() {
            return rollNumber;
        }

        public void setRollNumber(int rollNumber) {
            this.rollNumber = rollNumber;
        }

        public String getGrade() {
            return grade;
        }

        public void setGrade(String grade) {
            this.grade = grade;
        }

        @Override
        public String toString() {
            return "Roll Number: " + rollNumber + ", Name: " + name + ", Grade: " + grade;
        }
    }

    // Main class for the Student Management System
    private List<Student> students;
    private final String filePath = "students.dat"; // File path to store student data

    // Constructor
    public StudentManagementSystem() {
        students = new ArrayList<>();
        loadStudentsFromFile(); // Load data from file upon start
    }

    // Method to add a new student
    public void addStudent(Student student) {
        students.add(student);
        saveStudentsToFile(); // Save to file after adding
        System.out.println("Student added successfully.");
    }

    // Method to remove a student by roll number
    public void removeStudent(int rollNumber) {
        boolean removed = students.removeIf(student -> student.getRollNumber() == rollNumber);
        if (removed) {
            saveStudentsToFile(); // Save to file after removing
            System.out.println("Student removed successfully.");
        } else {
            System.out.println("Student with Roll Number " + rollNumber + " not found.");
        }
    }

    // Method to search for a student by roll number
    public Student searchStudent(int rollNumber) {
        for (Student student : students) {
            if (student.getRollNumber() == rollNumber) {
                return student;
            }
        }
        return null;
    }

    // Method to display all students
    public void displayAllStudents() {
        if (students.isEmpty()) {
            System.out.println("No students available.");
        } else {
            for (Student student : students) {
                System.out.println(student);
            }
        }
    }

    // Method to edit a student's details
    public void editStudent(int rollNumber, String newName, String newGrade) {
        Student student = searchStudent(rollNumber);
        if (student != null) {
            student.setName(newName);
            student.setGrade(newGrade);
            saveStudentsToFile(); // Save after editing
            System.out.println("Student information updated successfully.");
        } else {
            System.out.println("Student with Roll Number " + rollNumber + " not found.");
        }
    }

    // Method to save student data to file
    private void saveStudentsToFile() {
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(filePath))) {
            oos.writeObject(students);
        } catch (IOException e) {
            System.out.println("Error saving student data: " + e.getMessage());
        }
    }

    // Method to load student data from file
    @SuppressWarnings("unchecked")
    private void loadStudentsFromFile() {
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(filePath))) {
            students = (List<Student>) ois.readObject();
        } catch (IOException | ClassNotFoundException e) {
            System.out.println("No existing student data found.");
        }
    }

    // Input validation method to ensure that string inputs are not empty
    private String getValidatedInput(Scanner scanner, String prompt) {
        String input;
        do {
            System.out.print(prompt);
            input = scanner.nextLine();
            if (input.trim().isEmpty()) {
                System.out.println("Input cannot be empty. Please try again.");
            }
        } while (input.trim().isEmpty());
        return input;
    }

    // Main method to run the console-based interface
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        StudentManagementSystem sms = new StudentManagementSystem();
        boolean exit = false;

        while (!exit) {
            System.out.println("\n--- Student Management System ---");
            System.out.println("1. Add Student");
            System.out.println("2. Remove Student");
            System.out.println("3. Search Student");
            System.out.println("4. Edit Student");
            System.out.println("5. Display All Students");
            System.out.println("6. Exit");
            System.out.print("Enter your choice: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {
                case 1:
                    String name = sms.getValidatedInput(scanner, "Enter student name: ");
                    System.out.print("Enter roll number: ");
                    int rollNumber = scanner.nextInt();
                    scanner.nextLine(); // Consume newline
                    String grade = sms.getValidatedInput(scanner, "Enter student grade: ");
                    Student newStudent = new Student(name, rollNumber, grade);
                    sms.addStudent(newStudent);
                    break;

                case 2:
                    System.out.print("Enter roll number of student to remove: ");
                    int rollToRemove = scanner.nextInt();
                    sms.removeStudent(rollToRemove);
                    break;

                case 3:
                    System.out.print("Enter roll number of student to search: ");
                    int rollToSearch = scanner.nextInt();
                    Student foundStudent = sms.searchStudent(rollToSearch);
                    if (foundStudent != null) {
                        System.out.println("Student found: " + foundStudent);
                    } else {
                        System.out.println("Student not found.");
                    }
                    break;

                case 4:
                    System.out.print("Enter roll number of student to edit: ");
                    int rollToEdit = scanner.nextInt();
                    scanner.nextLine(); // Consume newline
                    String newName = sms.getValidatedInput(scanner, "Enter new name: ");
                    String newGrade = sms.getValidatedInput(scanner, "Enter new grade: ");
                    sms.editStudent(rollToEdit, newName, newGrade);
                    break;

                case 5:
                    System.out.println("Displaying all students:");
                    sms.displayAllStudents();
                    break;

                case 6:
                    exit = true;
                    System.out.println("Exiting Student Management System...");
                    break;

                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        }

        scanner.close();
    }
}
