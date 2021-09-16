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
i)Using rental payment amount, rental payment frequency, tenant start date and tenant end date to write a query that returns the sum of all payments from start date to end date.

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
