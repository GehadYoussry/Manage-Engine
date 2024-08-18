# Introduction
This project, related to a ticketing system called ManageEngine, has some issues that need to be resolved by data analysts to facilitate reports and analysis.
# How did we make the data:
Let's imagine we have two tables: the first table shows which group resolved each ticket, while the second table contains details about the specialists who responded to each ticket.
# Let's Create the First Table:
```sql
CREATE TABLE Tickets (
    TicketID INTEGER PRIMARY KEY AUTOINCREMENT,
    Team TEXT,
    AgentName TEXT,
    CreatedDate DATE,
    ResolvedDate DATE
);
INSERT INTO Tickets (Team, AgentName, CreatedDate, ResolvedDate) VALUES
('Payout', 'Alice', '2024-08-01', '2024-08-03'),
('Logistics', 'Bob', '2024-08-02', '2024-08-04'),
('Payin', 'Charlie', '2024-08-03', '2024-08-05'),
('Admin', 'David', '2024-08-01', '2024-08-04');
```
# Creating the second table:
```sql
CREATE TABLE Responses (
    ResponseID INTEGER PRIMARY KEY AUTOINCREMENT,
    TicketID INTEGER,
    ResponseText TEXT,
    ResponseDate DATE,
    FOREIGN KEY (TicketID) REFERENCES Tickets(TicketID)
);
INSERT INTO Responses (TicketID, ResponseText, ResponseDate) VALUES
(1, 'Initial response to the issue.', '2024-08-01'),
(1, 'Follow-up with the customer.', '2024-08-02'),
(2, 'Customer reported a new issue.', '2024-08-02'),
(2, 'Assigned to the support team.', '2024-08-03'),
(3, 'Technical team is investigating.', '2024-08-03'),
(4, 'Sales team reached out to the client.', '2024-08-01'),
(4, 'Client provided additional information.', '2024-08-02'),
(4, 'Issue resolved and confirmed with the client.', '2024-08-04');
```
# Combining The Two Queries:
```sql
WITH ResponseCounts AS (
    SELECT 
        t.Team,
        t.AgentName,
        t.TicketID,
        COUNT(r.ResponseID) AS ResponseCount
    FROM 
        Tickets t
    LEFT JOIN 
        Responses r ON t.TicketID = r.TicketID
    GROUP BY 
        t.Team, t.AgentName, t.TicketID
),
UniqueRespondents AS (
    SELECT 
        t.Team,
        t.TicketID,
        COUNT(DISTINCT r.ResponseID) AS UniqueRespondents
    FROM 
        Tickets t
    JOIN 
        Responses r ON t.TicketID = r.TicketID
    GROUP BY 
        t.Team, t.TicketID
    HAVING 
        COUNT(DISTINCT r.ResponseID) >= 1
)
SELECT 
    rc.Team,
    rc.AgentName,
    rc.TicketID,
    rc.ResponseCount
FROM 
    ResponseCounts rc
JOIN 
    UniqueRespondents ur ON rc.Team = ur.Team AND rc.TicketID = ur.TicketID
ORDER BY 
    rc.Team, rc.AgentName, rc.TicketID;

```
# Summary:
- **ResponseCounts:** Counts total responses per ticket by team and agent.
- **UniqueRespondents:** Identifies tickets with responses from more than one unique agent.
- **Final Query:** Combines the results to show response counts for tickets with multiple unique respondents.
# These queries provide a comprehensive view of ticket response activity, highlighting tickets with multiple agents' involvement.




