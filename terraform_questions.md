## TERRAFORM QUESTIONS

### How to limit the access control in the tfstatefile ?

- Add a DynamoDb table with the `lock_id`; it ensures that only 1 person could apply lock at a time.
- Interpretation would be like as follows.
  ![alt text](lock_id.png)

### How to determine if changes are done in an AWS Resources? How to find who did this ?

- If Done via AWS SERVICES we can use the =. CloudTrail
