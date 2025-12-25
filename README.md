# Лабораторная работа №1  
## Знакомство с IaaS, PaaS, SaaS сервисами в облаке на примере Amazon Web Services (AWS). Создание сервисной модели

---

## 1. Цель работы

Целью лабораторной работы является изучение принципов организации облачных сервисов и уровней абстракции в облаке на примере Amazon Web Services (AWS).

В ходе выполнения работы требовалось:
- разобраться в моделях предоставления облачных сервисов (IaaS, PaaS, SaaS);
- проанализировать строки биллинга AWS;
- научиться классифицировать потребление облачных ресурсов;
- построить сервисную модель, позволяющую агрегировать потребление от общего уровня к детальному.

---

## 2. Исходные данные

В качестве исходных данных использовался файл с правилами сопоставления биллинга AWS:

**Mapping Rules AWS team7.csv**

Файл содержит следующие ключевые поля:
- **Product Code** — идентификатор сервиса AWS в биллинге (например: AmazonS3, AmazonMSK, AmazonES);
- **Usage Type** — строковой шаблон, описывающий конкретный тип потребления или метрику биллинга  
например: `%TimedStorage-ByteHrs`, `%TimedStorage-GlacierByteHrs`, `%Requests%Tier1`,
`%DataTransfer%Bytes`, `%TrainingHour`, `%TPS-hours`.

Символ `%` в начале или конце значения означает, что до или после него может находиться произвольная строка.

---

## 3. Ход работы

### 3.1 Импорт и анализ данных

Файл с данными биллинга был импортирован в табличный редактор (Excel).  
Для каждой строки биллинга выполнялся анализ полей **Product Code** и **Usage Type**.

На основе документации AWS для каждой строки строилась сервисная иерархия следующего вида:


Где:
- **IT Tower** — верхнеуровневая технологическая область (Storage, Analytics, Security, AI/ML и др.);
- **Service Family** — семейство сервисов внутри области;
- **Service Type** — конкретный AWS-сервис;
- **Service Sub Type** — функциональный подтип или категория потребления;
- **Service Usage Type** — наиболее детальная метрика биллинга.

---

### 3.2 Построчный анализ биллинга

Для каждой строки биллинга последовательно выполнялись следующие шаги:
1. Определялся AWS-сервис на основании поля **Product Code**;
2. Анализировался тип потребления на основании **Usage Type**;
3. Заполнялись классификационные столбцы сервисной модели;
4. Дополнительные расходы (например, Tax) выносились в отдельную категорию.

---

## 4. Примеры классификации

### Amazon S3 — Requests

| IT Tower | Service Family | Service Type | Service Sub Type | Service Usage Type | Product Code | Usage Type |
|--------|---------------|--------------|------------------|--------------------|--------------|------------|
| Storage | Object Storage | Amazon S3 | Requests | Tier 1 Requests | AmazonS3 | %Requests%Tier1 |
| Storage | Object Storage | Amazon S3 | Requests | Tier 2 Requests | AmazonS3 | %Requests%Tier2 |
| Storage | Object Storage | Amazon S3 | Requests | Tier 3 Requests | AmazonS3 | %Requests%Tier3 |

---

### Amazon Personalize

| IT Tower | Service Family | Service Type | Service Sub Type | Service Usage Type | Product Code | Usage Type |
|--------|---------------|--------------|------------------|--------------------|--------------|------------|
| AI / ML | Machine Learning | Amazon Personalize | Inference | TPS-hours | AmazonPersonalize | %TPS-hours |
| AI / ML | Machine Learning | Amazon Personalize | Training | Training Hours | AmazonPersonalize | %TrainingHour |
| AI / ML | Machine Learning | Amazon Personalize | Data Ingestion | Data Volume | AmazonPersonalize | %DataIngestion |

---

### Amazon OpenSearch (AmazonES)

| IT Tower | Service Family | Service Type | Service Sub Type | Service Usage Type | Product Code | Usage Type |
|--------|---------------|--------------|------------------|--------------------|--------------|------------|
| Analytics | Search | Amazon OpenSearch | Instances | Compute | AmazonES | %ES:Instance% |
| Analytics | Search | Amazon OpenSearch | Block Storage | EBS GP2 | AmazonES | %ES:GP2-Storage% |
| Analytics | Search | Amazon OpenSearch | Data Transfer | Bytes | AmazonES | %DataTransfer%Bytes |

---

## 5. Итоговая классификация сервисов

В результате выполнения работы была построена сервисная модель, охватывающая следующие группы сервисов:

- **Storage**: Amazon S3 (Requests, Storage, Early Deletion, Tag Storage);
- **Analytics**: Amazon OpenSearch;
- **AI / ML**: Amazon Personalize, Amazon Polly;
- **Security**: Amazon Macie;
- **Streaming**: Amazon MSK;
- **Financial**: дополнительные расходы (Tax).

---

## 6. Вывод

В ходе лабораторной работы был выполнен анализ строк биллинга AWS и построена сервисная модель классификации потребления облачных ресурсов.

Было установлено, что:
- **Product Code** однозначно определяет используемый AWS-сервис;
- **Usage Type** отражает конкретный вариант потребления или метрику биллинга;
- сервисная модель позволяет агрегировать данные на разных уровнях — от IT Tower до конкретного типа потребления;
- дополнительные расходы (Tax) целесообразно выделять в отдельную категорию;
- единый стиль классификации обеспечивает удобство анализа и интерпретации данных.
