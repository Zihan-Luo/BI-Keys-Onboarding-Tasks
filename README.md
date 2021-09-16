# On-boarding Task
--
Task 1.a Display a list of all property names and their property id’s for Owner Id: 1426

    SELECT p.[Name] AS PropertyName,   
           p.Id AS PropertyID  
    FROM OwnerProperty AS op  
    INNER JOIN Property AS p ON op.PropertyId=p.Id  
    WHERE op.OwnerId=1426;  
![image](https://github.com/Zihan-Luo/Property-Analysis-Standard-Sprint--On-boarding-Task/blob/9b89695195954cc109fd5efbdbfbce24b9d970fb/images/1.png)

--
Task 1.b Display the current home value for each property in question a)

    SELECT p.[Name] AS PropertyName,  
           p.Id AS PropertyID,  
           phv.Value AS CurrentHomeValue  
    FROM OwnerProperty AS op  
    INNER JOIN Property AS p ON op.PropertyId=p.Id  
    INNER JOIN PropertyHomeValue AS phv	ON p.Id=phv.PropertyId  
    WHERE op.OwnerId=1426 AND phv.IsActive=1  
    ORDER BY p.[Name];  

--or

    SELECT p.[Name] AS PropertyName,  
           p.Id AS PropertyID,  
           pf.CurrentHomeValue  
    FROM Property AS p  
    INNER JOIN PropertyFinance AS pf ON pf.PropertyId=p.Id  
    INNER JOIN OwnerProperty AS op ON p.Id=op.PropertyId  
    WHERE op.OwnerId=1426 ORDER BY p.[Name];  
 ![image](https://github.com/Zihan-Luo/Property-Analysis-Standard-Sprint--On-boarding-Task/blob/749d5b8af6b467960e5314e458dad1ab3196499b/images/2.png)   
 
 --
Task 1.c For each property in question a), return the following:  
i) Using rental payment amount, rental payment frequency, tenant start date and tenant end date to write a query that returns the sum of all payments from start date to end date.

    SELECT p.[Name] AS PropertyName,  
           p.Id AS PropertyID,  
           CASE WHEN trt.[Name] ='Weekly' THEN (DATEDIFF(wk,tp.StartDate, tp.EndDate)*prp.Amount） 
           WHEN trt.[Name] ='Fortnightly' THEN ((DATEDIFF(wk,tp.StartDate, tp.EndDate)/2)*prp.Amount)  
           ELSE (DATEDIFF(m,tp.StartDate, tp.EndDate)*prp.Amount)  
           END AS SumOfPayments,  
           trt.[Name] AS RentalPaymentFrequency,   
           prp.Amount AS RentalPaymentAmount,  
           tp.StartDate AS TenantStartDate,  
           tp.EndDate AS TenantEndDate 
    FROM Property AS p  
    INNER JOIN OwnerProperty AS op ON p.Id=op.PropertyId  
    INNER JOIN PropertyRentalPayment AS prp ON p.Id=prp.PropertyId  
    INNER JOIN TargetRentType AS trt ON prp.FrequencyType=trt.Id  
    INNER JOIN TenantProperty AS tp ON p.Id=tp.PropertyId  
    WHERE op.OwnerId=1426;  
![image](https://github.com/Zihan-Luo/Property-Analysis-Standard-Sprint--On-boarding-Task/blob/749d5b8af6b467960e5314e458dad1ab3196499b/images/3.png)

--
Task 1.c For each property in question a), return the following:  
ii) Display the yield.


    SELECT p.[Name] AS PropertyName,  
           p.Id AS PropertyID,  
           phv.[Value] AS HomeValue,  
           (  
               (  
                   (CASE WHEN trt.[Name] ='Weekly' THEN (DATEDIFF(wk,tp.StartDate, tp.EndDate)*prp.Amount)  
                         WHEN trt.[Name] ='Fortnightly' THEN ((DATEDIFF(wk,tp.StartDate, tp.EndDate)/2)*prp.Amount)  
                         ELSE (DATEDIFF(m,tp.StartDate, tp.EndDate)*prp.Amount)  
                    END)  
               -ISNULL(SUM(pe.Amount),0)  
               )/phv.[Value]  
            )*100 AS Yield,  
            trt.[Name] AS RentalPaymentFrequency,  
            prp.Amount AS RentalPaymentAmount,  
            tp.StartDate AS TenantStartDate,  
            tp.EndDate AS TenantEndDate 
    FROM Property AS p  
    INNER JOIN OwnerProperty AS op ON p.Id=op.PropertyId  
    INNER JOIN PropertyRentalPayment AS prp ON p.Id=prp.PropertyId  
    INNER JOIN TargetRentType AS trt ON prp.FrequencyType=trt.Id  
    INNER JOIN TenantProperty AS tp ON p.Id=tp.PropertyId  
    INNER JOIN PropertyHomeValue AS phv	ON p.Id=phv.PropertyId  
    LEFT JOIN PropertyExpense AS pe ON p.Id=pe.PropertyId  
    WHERE op.OwnerId=1426 AND phv.IsActive=1  
    GROUP BY p.[Name],p.Id,phv.[Value],trt.[Name],prp.Amount,tp.StartDate,tp.EndDate;  
![image](https://github.com/Zihan-Luo/Property-Analysis-Standard-Sprint--On-boarding-Task/blob/4d66a5237d87a278b188d4e825acb9a4bdce4990/images/4.png)

--
Task 1.d.	Display all the jobs available in the marketplace (jobs that owners have advertised for service suppliers).

    SELECT j.Id AS JobID,  
           j.PropertyId,  
           j.OwnerId,  
           j.JobDescription,  
           Count(jm.Id) AS NoOfJobMedia  
    FROM Job AS j  
    INNER JOIN JobMedia AS jm ON j.Id=jm.JobId WHERE jm.IsActive=1  
    GROUP BY j.Id, j.PropertyId, j.OwnerId, j.JobDescription  
![image](https://github.com/Zihan-Luo/Property-Analysis-Standard-Sprint--On-boarding-Task/blob/c4cf8d33ade5465f5291e1cd2f433f10914c08d2/images/5.png)

--or

    SELECT DISTINCT j.Id AS JobID,  
           j.PropertyId,  
           j.OwnerId,  
           j.JobDescription,  
           jm.IsActive 
    FROM Job AS j  
    INNER JOIN JobMedia AS jm ON j.Id=jm.JobId WHERE jm.IsActive=1  
![image](https://github.com/Zihan-Luo/Property-Analysis-Standard-Sprint--On-boarding-Task/blob/c4cf8d33ade5465f5291e1cd2f433f10914c08d2/images/6.png)

--
Task 1.e.	Display all property names, current tenants first and last names and rental payments per week/ fortnight/month for the properties in question a).

    SELECT p.[Name] AS PropertyName,  
           Person.FirstName AS TenantFirstName,  
           Person.LastName AS TenantLastName,  
           prp.Amount AS RentalPaymentAmount,  
           trt.[Name] AS RentalPaymentFrequency 
    FROM OwnerProperty AS op  
    INNER JOIN Property AS p ON op.PropertyId=p.Id  
    INNER JOIN TenantProperty AS tp ON p.Id=tp.PropertyId  
    INNER JOIN Tenant AS t ON tp.TenantId=t.Id  
    INNER JOIN Person ON t.Id=Person.Id  
    INNER JOIN PropertyRentalPayment AS prp ON p.Id=prp.PropertyId  
    INNER JOIN TargetRentType AS trt ON prp.FrequencyType=trt.Id  
    WHERE op.OwnerId=1426;  
![image](https://github.com/Zihan-Luo/Property-Analysis-Standard-Sprint--On-boarding-Task/blob/c4cf8d33ade5465f5291e1cd2f433f10914c08d2/images/7.png)
