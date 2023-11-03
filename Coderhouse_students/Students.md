# 👨‍🎓Coderhouse students queries 👨‍🎓
## 👩‍💻Software: 
SQL SERVER MANAGEMENT STUDIO
## 📔Database: 
CoderHouse(3).bak consist in 6 tables, named in Spanish: "Area", "Asignaturas", "Encargado", "Estudiantes", "Profesiones", "Staff".

### 1. Indicate how many courses and careers the Data area has. Rename the new column as cant_assignatures.
First I need to check the ID of the Data area
 ```sql
SELECT * FROM Area
```
![Alt text](image.png)

Now I can do the count of courses and careers
```sql
SELECT COUNT(Area) AS cant_assignatures  
FROM Asignaturas 
WHERE Area = 5

```
![Alt text](image-1.png)

### 2. What is the name, identification document and telephone number of students who are professionals in agronomy and who were born between 1970 and 2000?.

```sql
SELECT Nombre, Documento, Telefono, Profesion, [Fecha de Nacimiento]
FROM Estudiantes
WHERE (Profesion = (
					SELECT ProfesionesID FROM Profesiones 
					WHERE (Profesiones = 'Agronomo Agronoma') AND
		[Fecha de Nacimiento] BETWEEN '1970-01-01' AND '2000-01-01'
					)
		)
```
![Alt text](image-2.png)


### 3. List the teachers who joined in 2021 and concatenate the first and last name fields with a hyphen (-). Example: Elba-Jimenez. Rename the new column  Nombres_Apellidos. The results in the new column must be in uppercase.
```sql
SELECT UPPER (CONCAT_WS ('-', Nombre, Apellido)) AS Nombres_Apellidos 
FROM Staff WHERE [Fecha Ingreso] BETWEEN '2021-01-01' AND '2021-12-31'
```
![Alt text](image-3.png)

### 4. Indicate the number of teachers and tutors in charge. Rename the column to CantEncargados. Remove the word Manager in each of the records. Rename the column as NuevoTipo.
```sql
SELECT COUNT(Encargado_ID) AS CantEncargados, TRIM('Encargado' FROM Tipo) AS NuevoTipo
FROM Encargado
GROUP BY Tipo
```

### 5. Indicate the average price of the carreers and courses per day. Rename the new column "Promedio". Order the averages from Highest to Lowest.

```sql
SELECT AVG(Costo) AS Promedio, Tipo FROM Asignaturas GROUP BY Tipo ORDER BY Tipo
```
![Alt text](image-5.png)

### 6. Calculate the age of the students in a new column. Rename the new column Edad. Filter only those who are over 18 years old. Sort from Lowest to Highest

```sql
SELECT DATEDIFF(year, [Fecha de Nacimiento], GETDATE()) AS Edad 
FROM Estudiantes 
WHERE DATEDIFF(year, [Fecha de Nacimiento], GETDATE()) >= 18
ORDER BY DATEDIFF(year, [Fecha de Nacimiento], GETDATE()) 
```
![Alt text](image-6.png)

### 7. List the Name, email, class and date of entry of staff members who contain ".edu" email and their TeacherID is greater than or equal to 100
```sql
SELECT Nombre, Correo, [Fecha Ingreso], Camada FROM Staff
WHERE DocentesID> 100 AND (RIGHT(Correo, 4)) = '.edu'
```
![Alt text](image-7.png)

### 8. It is required to know the document, address, zip code and name of the first students who registered on the platform
```sql
SELECT TOP 5 Documento, Domicilio, [Codigo Postal], Nombre
FROM Estudiantes
ORDER BY [Fecha Ingreso]
```
![Alt text](image-8.png)

### 9. Indicate the name, surname and identification document of the teachers and tutors who have UX subjects
```sql
SELECT Nombre, Apellido, Documento FROM Staff 
WHERE Asignatura IN (
	SELECT AsignaturasID FROM Asignaturas WHERE Nombre LIKE '%UX%'
					)
 ```
 ![Alt text](image-9.png)
### 10. Calculate the 25% increase for the subjects in the marketing area for the morning shift, all the fields must be brought, The new cost must be in decimal with 3 digits. Rename the percentage calculation to Porcentaje and the sum of the cost plus the percentage to NuevoCosto
```sql
SELECT AsignaturasID, 
		Nombre,		
		Tipo, 
		Jornada, 
		Costo, 
		Area, 
		CAST((Costo*0.25) AS DECIMAL (10,3)) AS Porcentaje,
		CAST((Costo * 1.25) AS DECIMAL(10,3)) AS NuevoCosto 
FROM Asignaturas WHERE Jornada = 'Manana'
```
![Alt text](image-10.png)

### 11. Only for web development asignatures, indicate the number of teachers who teach by shift and add the costs. The result must contain all the fields of the first table. Rename the column for calculating the number of teachers as cant_teachers and the column for the sum of costs as suma_total

```sql
SELECT	COUNT (DocentesID) AS cant_docentes, 
		Jornada, 
		SUM (Costo) AS suma_total
FROM Asignaturas as A LEFT JOIN Staff ON AsignaturasID = Asignatura
WHERE A.Nombre Like '%Web%'
GROUP BY Jornada
```
![Alt text](image-11.png)

### 12. List the ID, name, last name and how many teachers are assigned to each person in charge. Then filter the managers who have a result of 0 since they are the managers who DO NOT have a teacher assigned. Rename the operation field as Cant_Teachers.

```sql
SELECT Encargado_ID, E.Nombre, E.Apellido, COUNT(DocentesID) AS Cant_Docentes
FROM Encargado AS E LEFT JOIN Staff AS S
ON Encargado_ID = Encargado
GROUP BY Encargado_ID, E.Nombre, E.Apellido --Columns with no aggregation must be group by
HAVING COUNT(DocentesID) = 0 -- The WHERE clause is for existing fields and the HAVING clause is applied to the rows in the result set. 
```

### 13. List all the data for subjects that do not have an assigned teacher. The query model must start from the teachers table. 
```sql
SELECT AsignaturasID, A.Nombre, A.Tipo, A.Jornada, A.Costo
FROM Staff AS S RIGHT JOIN Asignaturas AS A  
ON Asignatura = AsignaturasID
GROUP BY AsignaturasID, A.Nombre, A.Tipo, A.Jornada, A.Costo
HAVING COUNT (DocentesID) = 0
```

![Alt text](image-12.png)

### 14. We want to know the following information about teachers. The full name concatenate the first and last name. Rename "NombresCompletos", the document, make a calculation to know the start month. Rename  "meses_ingreso", the name and telephone of the person in charge: Rename "NombreEncargado" and "TelefonoEncargado", the name of the course or career, the day and the name of the area. You only want to view teachers that have been there for more than 3 months. Order the months from start from highest to lowest.
```sql
SELECT CONCAT(S.Nombre,' ', S.Apellido) AS NombresCompletos,
		S.Documento,
		DATEDIFF (MONTH,[Fecha Ingreso], (GETDATE()) ) AS meses_ingreso,
		E.Nombre AS  NombreEncargado,
		E.Telefono AS TelefonoEncargado,
		A.Nombre,
		A.Jornada,
		Ar.Nombre
FROM Staff AS S JOIN Encargado AS E 
ON Encargado = Encargado_ID 
JOIN Asignaturas AS A 
ON Asignatura = AsignaturasID 
JOIN Area AS Ar
ON Area = AreaID
ORDER BY DATEDIFF (MONTH,[Fecha Ingreso], (GETDATE()) )
```

### 15. Create a unified list with name, surname, document and indicate which base it corresponds to. 
 ```sql
 SELECT S.Nombre, S.Apellido, S.Documento, 'Staff' AS Marca FROM Staff AS S
UNION
SELECT E.Nombre, E.Apellido, E.Documento, 'Encargado' AS Marca FROM Encargado AS E
UNION
SELECT Stu.Nombre, Stu.Apellido, Stu.Documento, 'Estudiantes' AS Marca FROM Estudiantes AS Stu
 ```
![Alt text](image-13.png)


