# Payroll Management System

## Overview

This Payroll Management System is a Java-based application built using NetBeans IDE, backed by a MySQL database. It is designed to help companies manage employee payroll efficiently by automating the process of employee record management, payroll calculation, and pay slip generation. The system is GUI-based, utilizing Java Swing for the frontend and MySQL for the database backend.

## System Architecture

The Payroll Management System follows a modular architecture, where each component has a distinct responsibility. The high-level structure of the system is organized into the following key modules:

### Main Components:
1. **User Authentication**: Handles login functionality and role-based access control for administrators.
2. **Employee Management**: Manages employee records (Add, Update, Delete, Search).
3. **Payroll Management**: Handles salary details, allowance calculations, and deductions.
4. **Pay Slip Generation**: Calculates gross pay, deductions, and net pay, and allows the printing of pay slips.
5. **Database Interaction**: Provides a layer for executing SQL queries and updating the MySQL database.

### Interaction Flow:
- Users interact with the system via a GUI (Java Swing).
- Upon login, the system verifies credentials and grants access to the administrator.
- Administrators manage employee data, payroll details, and generate pay slips.
- All data is fetched from or updated in the MySQL database through SQL queries.

---

## Core Components and Modules

### 1. **User Authentication (`User.java`, `Login.java`)**
   - **Purpose**: Handles user login and verifies if the entered username and password match an existing record in the database.
   
   #### Key Methods:
   - `isUser()`: Queries the database to check if the username and password combination exists.
     ```java
     public ResultSet isUser() {
         String sql = "SELECT * FROM User WHERE userName='" + userName + "' AND password='" + password + "'";
         return objSQLRun.sqlQuery(sql);
     }
     ```
   - **Login Form**: Handles user input and performs login action.
     ```java
     btn_loginActionPerformed(evt): Verifies the user credentials and loads the Home page.
     ```

### 2. **Employee Management (`Employee.java`, `Person.java`)**
   - **Purpose**: Manages employee information such as name, designation, department, and salary details.

   #### Key Methods:
   - `setEmployeeDetails()`: Sets employee attributes (first name, last name, designation, department, etc.) based on data from the database.
     ```java
     objEmployee.setFname(resSet.getString("fname"));
     objEmployee.setLname(resSet.getString("lname"));
     objEmployee.setDesignation(resSet.getString("designation"));
     ```
   - **Inheritance**: `Employee` class extends `Person` class, inheriting basic personal details like name, address, and contact numbers.

### 3. **Payroll Management (`PaySlip.java`, `Payroll.java`)**
   - **Purpose**: Handles salary-related calculations, including allowances, deductions, and net pay.

   #### Key Methods:
   - `getPayDetails(String empId)`: Retrieves employee salary details and calculates gross pay, deductions, and net pay.
     ```java
     grossPay = (objEmployee.getSalAmount() + objPayroll.getTravelAmount() + objPayroll.getFoodAmount() + objPayroll.getBonusAmount());
     totalDeductions = (objPayroll.getEpfAmount() + objPayroll.getTaxAmount() + objPayroll.getPayeAmount());
     netPay = (grossPay - totalDeductions);
     return true;
     ```
   - **Attributes**:
     - `grossPay`: Total earnings before deductions.
     - `totalDeductions`: Sum of all deductions such as EPF, tax, and PAYE.
     - `netPay`: Final pay after deductions.

### 4. **Pay Slip Generation and Printing (`Print.java`)**
   - **Purpose**: Renders the pay slip for an employee and prints it.

   #### Key Methods:
   - `print()`: Scales and prints the component (e.g., pay slip) on the specified page format.
     ```java
     public int print(Graphics graphics, PageFormat pageFormat, int pageIndex) throws PrinterException {
         if (pageIndex > 0) return Printable.NO_SUCH_PAGE;
         else { 
             // Renders the pay slip
             Graphics2D graphics2D = (Graphics2D) graphics;
             graphics2D.translate(pageXStart, pageYStart);
             graphics2D.scale(xRatio, yRatio);
             COMPONENT.paint(graphics2D);
             return Printable.PAGE_EXISTS;
         }
     }
     ```

### 5. **Database Interaction (`SQLRun.java`)**
   - **Purpose**: Facilitates communication with the MySQL database, providing methods for running SQL queries and updates.

   #### Key Methods:
   - `sqlQuery(String sql)`: Executes a SQL `SELECT` statement and returns the result set.
     ```java
     public ResultSet sqlQuery(String sql) {
         try {
             statement = DbConnection.getDbConnection().createStatement();
             return statement.executeQuery(sql);
         } catch (SQLException ex) {
             // Error handling
             return null;
         }
     }
     ```
   - `sqlUpdate(String sql)`: Executes a SQL `UPDATE` or `INSERT` statement.
     ```java
     public int sqlUpdate(String sql) {
         try {
             statement = DbConnection.getDbConnection().createStatement();
             return statement.executeUpdate(sql);
         } catch (SQLException ex) {
             // Error handling
             return 0;
         }
     }
     ```

---

## Example Code Snippets

### Login Example
Here is an example showing how the login functionality works:
```java
User objUser = new User();
objUser.setUserName("admin");
objUser.setPassword("password123");

ResultSet resSet = objUser.isUser();

if (resSet.next()) {
    // User authenticated
    Home homeFrame = new Home();
    homeFrame.setVisible(true);
} else {
    // Authentication failed
    JOptionPane.showMessageDialog(null, "Invalid username or password", "ERROR", 0);
}
```
### Pay Slip Calculation Example
This example demonstrates how gross pay, deductions, and net pay are calculated for an employee:

```java
public boolean getPayDetails(String empId) {
    ResultSet resSet = objSQLRun.sqlQuery("SELECT * FROM employee e, salary_details s WHERE e.empId='" + empId + "' AND e.empId=s.empId");
    if (resSet.next()) {
        // Set employee details
        objEmployee.setFname(resSet.getString("fname"));
        objEmployee.setSalAmount(resSet.getDouble("salAmount"));
        
        // Set payroll details
        objPayroll.setBonusAmount(resSet.getDouble("bonus"));
        objPayroll.setEpfAmount(resSet.getDouble("epf"));

        // Calculate
        double grossPay = objEmployee.getSalAmount() + objPayroll.getBonusAmount();
        double totalDeductions = objPayroll.getEpfAmount();
        double netPay = grossPay - totalDeductions;
        return true;
    } else {
        JOptionPane.showMessageDialog(null, "No Record Found for Employee ID: " + empId, "ERROR", 0);
        return false;
    }
}
```
## Database Setup
To run the Payroll Management System, you need to configure the MySQL database:

1. Install XAMPP (or any MySQL server).
2. Import the provided SQL database file into MySQL.
3. Ensure that your database connection details in DbConnection.java match your local MySQL setup.

## Conclusion
This Payroll Management System provides a complete solution for managing employee records, calculating payroll, and generating pay slips. By utilizing a modular structure, it ensures maintainability and ease of use.
