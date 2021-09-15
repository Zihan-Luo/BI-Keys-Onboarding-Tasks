# On-boarding Task
--
Task 1.a Display a list of all property names and their property id’s for Owner Id: 1426

    SELECT p.[Name] AS PropertyName,   
           p.Id AS PropertyID  
    FROM OwnerProperty AS op  
    INNER JOIN Property AS p ON op.PropertyId=p.Id  
    WHERE op.OwnerId=1426;  
![image](https://github.com/Zihan-Luo/Property-Analysis-Standard-Sprint--On-boarding-Task/blob/9b89695195954cc109fd5efbdbfbce24b9d970fb/images/1.png)
