import java.io.*;
import java.util.*;

// PART B: Student class for serialization
class Student implements Serializable {
    private String name;
    private int rollNo;

    public Student(String name, int rollNo) {
        this.name = name;
        this.rollNo = rollNo;
    }

    public String toString() {
        return "Student{name='" + name + "', rollNo=" + rollNo + "}";
    }
}

// PART C: Employee class
class Employee implements Serializable {
    private int empId;
    private String name;
    private double salary;

    public Employee(int empId, String name, double salary) {
        this.empId = empId;
        this.name = name;
        this.salary = salary;
    }

    public String toString() {
        return "Employee{ID=" + empId + ", Name='" + name + "', Salary=" + salary + "}";
    }
}

public class Main {
    private static final String EMP_FILE = "employees.dat";

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        // PART A: Autoboxing and Unboxing
        System.out.println("=== Part A: Sum of Integers Using Autoboxing/Unboxing ===");
        Integer a = 10;  // Autoboxing
        Integer b = 20;
        int sum = a + b; // Unboxing
        System.out.println("Sum = " + sum);

        // PART B: Serialization and Deserialization
        System.out.println("\n=== Part B: Serialization and Deserialization of Student Object ===");
        Student s1 = new Student("Ashish", 101);
        try {
            // Serialization
            ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("student.ser"));
            out.writeObject(s1);
            out.close();

            // Deserialization
            ObjectInputStream in = new ObjectInputStream(new FileInputStream("student.ser"));
            Student s2 = (Student) in.readObject();
            in.close();

            System.out.println("Deserialized Student: " + s2);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // PART C: Menu-Based Employee Management System
        System.out.println("\n=== Part C: Menu-Based Employee Management System ===");
        int choice;
        do {
            System.out.println("\n1. Add Employee");
            System.out.println("2. Display All Employees");
            System.out.println("3. Exit");
            System.out.print("Enter your choice: ");
            choice = sc.nextInt();
            sc.nextLine(); // consume newline

            switch (choice) {
                case 1:
                    System.out.print("Enter Employee ID: ");
                    int id = sc.nextInt();
                    sc.nextLine();
                    System.out.print("Enter Employee Name: ");
                    String name = sc.nextLine();
                    System.out.print("Enter Salary: ");
                    double salary = sc.nextDouble();
                    sc.nextLine();
                    Employee emp = new Employee(id, name, salary);
                    addEmployee(emp);
                    System.out.println("Employee added successfully!");
                    break;
                case 2:
                    displayEmployees();
                    break;
                case 3:
                    System.out.println("Exiting Employee Management System...");
                    break;
                default:
                    System.out.println("Invalid choice!");
            }
        } while (choice != 3);
        sc.close();
    }

    // Method to add employee to file
    private static void addEmployee(Employee emp) {
        try {
            ObjectOutputStream out;
            File f = new File(EMP_FILE);
            if (f.exists()) {
                // Append to existing file
                out = new AppendableObjectOutputStream(new FileOutputStream(f, true));
            } else {
                out = new ObjectOutputStream(new FileOutputStream(f));
            }
            out.writeObject(emp);
            out.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    // Method to display employees
    private static void displayEmployees() {
        try {
            ObjectInputStream in = new ObjectInputStream(new FileInputStream(EMP_FILE));
            System.out.println("\n--- Employee List ---");
            while (true) {
                Employee emp = (Employee) in.readObject();
                System.out.println(emp);
            }
        } catch (EOFException e) {
            // End of file reached
        } catch (FileNotFoundException e) {
            System.out.println("No employees found.");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // Custom class to append objects without writing header again
    static class AppendableObjectOutputStream extends ObjectOutputStream {
        public AppendableObjectOutputStream(OutputStream out) throws IOException {
            super(out);
        }
        @Override
        protected void writeStreamHeader() throws IOException {
            reset();
        }
    }
}
