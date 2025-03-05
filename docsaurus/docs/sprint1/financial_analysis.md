---
sidebar_position: 2
label: "Análise financeira"
---

# Financial Analysis

## Introduction

Financial analysis is a crucial process that allows companies and investors to assess the feasibility, stability, and profitability of a business or project. It involves reviewing and evaluating a company's financial statements, such as balance sheets, income statements, and cash flows. This analysis provides valuable insights into the company's financial health, including its ability to generate profit, manage debt, and finance its operations.

## MVP Cost Structure

In this document, we will analyze the cost structure of the Minimum Viable Product (MVP) for the project. This analysis includes the costs associated with the development, implementation, and maintenance of the MVP, as well as the costs of the team, cloud services, and outsourcing. By understanding the financial implications of the project, we can make informed decisions about resource allocation, budgeting, and investment. Further analysis will be done to evaluate the operating cost

### Team Costs

here we have the average cost of a 5 person developer team, these figures were obtained at the [glassdoor website](https://www.glassdoor.com.br/Sal%C3%A1rios/index.htm) and are based on the average salary of a developer in Brazil. The team is composed of 1 Junior, 3 Plenos and 1 Senior developer. The total annual cost of the team is R$1,056,000.00, with a monthly cost of R$88,000.00.These analysis will not apply to the MVP as me and my colleagues will be working on the project for free.

| Cargo     | quantity | employ cost per year   | employ cost per month |
|-----------|------------|----------------|--------------|
| Juniors   | 1          | R$54.000,00    | R$4.500    |
| Plenos    | 3          | R$93.600,00   | R$7.800,00  |
| Seniors   | 1          | R$150.000,00   | R$12.500   |
| **Total** | **4**      | **R$484.800,00** | **R$40.400** |

we also have a series of taxes when it comes to employment

| Tax | Percentage  | 
|-----|-------------|
| FGTS | 8%         |
| INSS | 20%        |
| 13th salary | 8.33% |
| Sistema S | 3.3%    |
| Salário Educação | 2.5% |
| Total | 42.13%    |

these additional costs will be added to the total cost of the team resulting in a total of RS 689.046,24 per year and R$57,420.52 per month.
## Cloud spenditures
Here we have  estimative of the cost of the cloud services that will be used in the project. The services are AWS Elastic Load Balancing, Amazon EC2, Amazon VPC, AWS RDS, Mongo and IBM Watson. The total cost of these services is R$35,379.36 per year, with a monthly cost of $2,948.28. The exchange rate used for this calculation is R$5.05.



 ### AWS Elastic Load Balancing  
    - **Application Load Balancer: 
    -  $0.0225 per Application Load Balancer-hour 
      - $0.008 per Load Balancer Capacity Unit (LCU) per hour 
     - **Network Load Balancer**: 
    -  $0.0225 per Network Load Balancer-hour 
      - $0.006 per Network Load Balancer Capacity Unit (NLCU) per hour 
     - **Gateway Load Balancer**: 
      - $0.0125 per Gateway Load Balancer-hour 
      - $0.004 per Gateway Load Balancer Capacity Unit (GLCU) per hour 
     - **Classic Load Balancer**: 
      - $0.025 per Classic Load Balancer-hour 
      - $0.008 per GB of data processed 
     *Note: Standard AWS data transfer charges apply in addition to the above.* 
     [Pricing Details](https://aws.amazon.com/elasticloadbalancing/pricing/) 

### Amazon EC2 
- On-Demand Instances: 
  Pay for compute capacity by the hour or second (minimum of 60 seconds) with no long-term commitments. 
  Pricing varies by instance type, region, and operating system. 
 [On-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand/)

### Amazon VPC  
- NAT Gateway: 
  $0.045 per NAT Gateway-hour 
  $0.045 per GB data processed by the NAT Gateway 
 *Note: Additional data transfer charges may apply based on the source/destination and amount of data transferred.* 
 [VPC Pricing](https://aws.amazon.com/vpc/pricing/)                                                                                                                                                                                                                                  |
### **AWS RDS** 
 - **On-Demand Instances**: 
  Pay for compute capacity per hour your DB instance runs, with no long-term commitments. 
  Pricing varies based on database engine, instance type, and region. 
 - **Reserved Instances**: 
  - Option to reserve a DB instance for a one- or three-year term with significant discounts compared to On-Demand rates. <br/> [RDS Pricing](https://aws.amazon.com/rds/pricing/)|

### **MongoDB** 
 - **MongoDB Atlas**:
 - **Shared Instances**:
   - Starting at $9 per month.
 - **Dedicated Clusters**:
   - Starting at $0.08 per hour (~$60 per month).
 - **Serverless Instances**:
   - $0.10 per million Read Processing Units (RPUs) for the first 50 million RPUs per day.
   - $0.20 per GB of storage per month.
   - $2.50 per restore hour.
 - **Data Transfer Costs**:
   - Regional: $0.01 per GB.
   - Cross-Region: $0.02 to $0.20 per GB.
   - Public Internet: $0.09 to $0.20 per GB. <br/> [MongoDB Atlas Pricing](https://www.mongodb.com/pricing) |

1. Amazon EC2 (Elastic Compute Cloud):

Assuming you require two t3.medium instances (each with 2 vCPUs and 4 GiB RAM) running 24/7 to handle application and database workloads:​

On-Demand Pricing: Approximately $0.0416 per hour per instance in the US East (Ohio) region.​

Monthly Cost per Instance: $0.0416/hour * 730 hours/month = ~$30.37​

Annual Cost for Two Instances: $30.37 * 2 instances * 12 months = ~$728.88​

1. Amazon RDS (Relational Database Service):

For a managed PostgreSQL database using a db.t3.medium instance:​

On-Demand Pricing: Approximately $0.0416 per hour.​

Monthly Cost: $0.0416/hour * 730 hours/month = ~$30.37​
AWS

Annual Cost: $30.37 * 12 months = ~$364.44​

3. Amazon S3 (Simple Storage Service):

Assuming an average storage requirement of 1 GB per user:​
reddit.com

Total Storage: 500 users * 1 GB/user = 500 GB​

Standard Storage Pricing: $0.023 per GB per month.​


Monthly Storage Cost: 500 GB * $0.023/GB = $11.50​

Annual Storage Cost: $11.50 * 12 months = $138​

1. Data Transfer (Bandwidth):

Assuming each user generates 2 GB of data transfer per month:​

Total Monthly Data Transfer: 500 users * 2 GB/user = 1,000 GB (1 TB)​

First 1 GB per month: Free​
reddit.com

Next 9.999 TB per month: $0.09 per GB​

Monthly Data Transfer Cost: (1,000 GB - 1 GB) * $0.09/GB = ~$89.91​
reddit.com

Annual Data Transfer Cost: $89.91 * 12 months = ~$1,078.92​

5. AWS Support Plan:

Opting for the Developer Support Plan, which is the greater of $29 per month or 3% of monthly AWS charges:​

Estimated Monthly AWS Charges: $30.37 (EC2) + $30.37 (RDS) + $11.50 (S3) + $89.91 (Data Transfer) = ~$162.15​

Support Cost: 3% of $162.15 = ~$4.86 (since this is less than $29, the minimum $29 fee applies)​

Annual Support Cost: $29 * 12 months = $348​

Total Estimated Annual Cost:

EC2 Instances: ~$728.88​

RDS Instance: ~$364.44​

S3 Storage: ~$138​

Data Transfer: ~$1,078.92​

Support Plan: ~$348​
AWS

Grand Total: ~$2,658.24

We use MongoDB `Dedicated` plan, for 4 TB of data. This is a very confartable margin for the project, as the data will be stored in a very efficient way. 

## Custo total do MVP

Para representar todos os gastos de forma resumida, este quadro apresenta uma visão detalhada dos custos anuais e mensais associados ao desenvolvimento e manutenção do Produto Mínimo Viável (MVP), sem considerar a terceirização de serviços. Os custos incluem a equipe de engenheiros, arquitetura, implementação do projeto e manutenção do modelo, totalizando R$2.824,331.54 por ano, o que resulta em um custo mensal de R$235,360.96. Além disso, é mencionado um imposto de 20%, resultando em R$3.389.197,84. Este resumo financeiro é crucial para compreender o investimento necessário para o lançamento do MVP, permitindo uma análise aprofundada da viabilidade financeira do projeto antes de prosseguir com a terceirização ou outras expansões.


Now we will show an 
| Item                   | Preço por Ano    | Preço por Mês  |
|------------------------|------------------|----------------|
| developers  | R$35,379.36   | R$2.948,28    |
| cloud | 2,658.24 USD|  221,52 USD     |
| Dolar | R$5.77 | R$5.77 |
| **Total**     | **R$ 50.717,4048** | **R$ 4.226,4504**|


*Dolar used was at 5,77 reais*
