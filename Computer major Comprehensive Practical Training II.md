

## 					HUBEI UNIVERSITY OF AUTOMOTIVE  TECHNOLOGY

###  											SCHOOL OF ELECTRICAL AND INFORMATION TECHNOLOGY 

​									 DEPARTMENT OF COMPUTER SCIENCE AND TECHNOLOGY

# Computer major Comprehensive Practical Training II



#### 

## 												Student Report



#### 																				Student Id: 202041201

#### 																				Student Name: Ndombasi Joao Andre Diakusala 

​																Teacher: Qi Xin

​													

1. ## Objective

   This report is the comprehensive discussion of SSM(SPRING + SPRING-MVC + MyBatis) framework. It uses a concreate example to demonstrate how everything is working together from database(DAO) layer to presentation layer(Front-End).

   It emphasizes on the MVC project style, CRUD and REST API. It brings forth the best framework for Java EE web application development and modern adapted solutions for easy and quick implementation of tasks.

   

2. ## Project Organization

   The demo project is named ssm-crud and project files are organized as follow: 

   ![project_files](C:\Users\Alamin\Desktop\folder\report res\images\project_files.png)

### Files Description

Files, as shown on the image above, are mainly divided into two categories: 

1. ### CONFIGURATION FILES

   These XML files are set to set all the logic behind the SSM logic.

   - ​	POM: project maven dependencies

     ​	![maven_dependencies](C:\Users\Alamin\Desktop\folder\report res\images\maven_dependencies.png)

   

   - ApplicationContext.xml : gives the business logic. configure transaction enhancements which are made by Spring **beans definition**, **context component scan** and **AOP configurations**.

     

   - dbconfig.properties: it sets the JDBC properties (JDBC: JAVA DATABASE CONNECTIVITY) 

     

   - logback.xml: It gives the detailed logging of debugging process when using the databse services.

     

   - mybatis-config.xml: set the DBMS (database management system) used, in thiis case MySQL and enables the page helper plugin.

     

   - rebel.xml: This is the JRebel configuration file. It maps the running application to your IDE workspace, enabling JRebel reloading for this project.

   - spring-mvc.xml: sets the component-scan base package for spring and the default servlet handler. 

     

2. ### JAVA FILES 

   This is the java code used to implement the system. I gives 5 layers for data processing in and out the server.

   ![java package](C:\Users\Alamin\Desktop\folder\report res\images\java package.png)

   

   - Bean or VO: java classes to define the objects that are used to encapsulate the data in transactions. 

     In occurrence, Department.java and DepartmentExample.java

     

   - DAO: Data Access Object, A java interface that is mapped with a class configuration mapper file to sets all the signatures of methods used by the referenced bean. In occurrence, EmployeeMapper.xml and DepartmentMapper.xml.

     

   - Controller: It is the spring version of servlets, It gets requests and sends back responses. spring annotation @Controller

     

   - Service: sets the way for data flow in and outside the database. spring annotation @Service. In occurrence, EmployeeService.java and DepartmentService.java.

     

   - Util: MyBatisTest.java, the MyBatis framework reverse generation technology is used to generate all the beans, mapping interfaces and mapping xml files. 

     > ![mappings](C:\Users\Alamin\Desktop\folder\report res\images\mappings.png)

   > ### LOGIC
   >
   > the logic is fairly simple. 
   >
   > A request is sent from the browser and the DispatcherServlet filters the controllers and passes the request object to the right referenced URL and method (GET, POST, UPDATE, DELETE) which defines CRUD API. and the controller calls the Service layer for implementation of the operation, the service calls for mapper file of DAO layer for database resources and the result of the operations is given by rebel.
   >
   > When the response is readym, data come from database and upto controller that will pass the objects to the Model(session) and the view mapping will help call the right view page.

3. ### VIEW FILES

   ### Home Page ---> index.jsp

   ![homepage](C:\Users\Alamin\Desktop\folder\report res\images\homepage.png)

   

   ### EMPLOYEE

   - Employee Home Page ---> employee.jsp. list of all employees with pagination

     ![list_employees](C:\Users\Alamin\Desktop\folder\report res\images\list_employees.png)

   - Add Employee Page: a form to register a new employee.

     ![add_employee](C:\Users\Alamin\Desktop\folder\report res\images\add_employee.png)

> ​	RESULT id=1003
>
> ![add_emp_success](C:\Users\Alamin\Desktop\folder\report res\images\add_emp_success.png)

- Update Employee  id: 1003 name changes to testing and gender to Female

> ​	RESULT
>
> ![update_emp_succcess](C:\Users\Alamin\Desktop\folder\report res\images\update_emp_succcess.png)

- Search Emplyee fuzzy search key=am

> ​	RESULT id=1002 and name = amin
>
> ![search_emp_success](C:\Users\Alamin\Desktop\folder\report res\images\search_emp_success.png)



- Delete Employee id: 1003 is removed from the table.

> ​	RESULT
>
> ![list_employees](C:\Users\Alamin\Desktop\folder\report res\images\list_employees.png)

​	

- Batch Delete Employee id: 999 and 1001 will be removed from the table.

> ![batch_delete_employees](C:\Users\Alamin\Desktop\folder\report res\images\batch_delete_employees.png)

> ​			RESULT employees id=999 and id=1001 are removed from the table.
>
> ![batch_delete_emp_success](C:\Users\Alamin\Desktop\folder\report res\images\batch_delete_emp_success.png)

### DEPARTMENT

- Department Home Page ---> department.jsp.  list of all departments with pagination.

  ![list_deps](C:\Users\Alamin\Desktop\folder\report res\images\list_deps.png)

- Add Department Page: a form to register a new department.

> ​	RESULT id=2056
>
> ![add_dep_success](C:\Users\Alamin\Desktop\folder\report res\images\add_dep_success.png)

- Update Department id: 2056 name changes to test changed

> ​	RESULT
>
> ![update_dep_success](C:\Users\Alamin\Desktop\folder\report res\images\update_dep_success.png)

- Search Department  fuzzy search key=test

> ​	RESULT id=3 and id=2056
>
> ![dep_search_success](C:\Users\Alamin\Desktop\folder\report res\images\dep_search_success.png)

​	

- Batch Delete Departments id: 3 and 2056 will be removed from the table.

> ![batch_delete_dep](C:\Users\Alamin\Desktop\folder\report res\images\batch_delete_dep.png)



## PERSONAL EXPERIENCE

Before this course, I could develop simple web apps with mvc nd maven dependency tool but it was not up to date as the java EE is on the market now. But this course has given me a very rich view to:

- AOP(Aspect Oriented Programming), something that I didn't know before. but the teacher gave us more examples to demonstrate the logic.
- Spring framework: with IoC (Inversion of Control) and DI(dependency injection). to realize the loose coupling between beans which augments stability in the program, since no class is dependent directly on the other.
- MVC: The user sends a request to the server through the View layer, and the request is received by the Controller in the server. The Controller invokes the corresponding Model layer to process the request, and returns the result to the Controller, and the Controller finds the corresponding view according to the request processing result. The data is rendered and the final response is sent back to the browser
- Spring MVC: is a complete set of solutions provided by Spring for the development of presentation layer. After the evolution of presentation layer framework, such as Strust, WebWork, Strust2 and so on, SpringMVC is generally chosen as the first choice for presentation layer development in Java EE projects
- RESTFUL  API: a design style and development method of network applications (**Re**presentational **S**tate **T**ransfer，It is the presentation layer resource state transfer)
- HttpMessageConverter: this is one of the most interresting plugins I learned. It is used to convert request packets into Java objects or Java objects into response packets. It provides two annotations and two entity class which are @RequestBody，@ResponseBody，RequestEntity，ResponseEntity.
- MyBatis: is a first class persistence framework with support for custom SQL, stored procedures and advanced mappings. It helps us by eliminating almost all of the JDBC code and manual setting of parameters and retrieval of results. MyBatis can use simple XML or Annotations for configuration and map primitives, Map interfaces and Java POJOs (Plain Old Java Objects) to database records.
- CRUD: 
- Mybatis provides corresponding annotations for CRUD operations which have annotations: @Select @Insert @Update @Delete. it allows simplify these 4 main operations in a database.
-  Spring Unit Testing and logback: these two are my favorites debugging code tools. they help log out the whole process and detailed verion of results and failures the program may encounter while running.
- Idea: I could use this IDE but I learned much more details about it. I can use database and web development tools inside. 