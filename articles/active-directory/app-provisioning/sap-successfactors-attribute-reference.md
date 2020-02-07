---
title: Odwołanie do atrybutu SAP SuccessFactors | Microsoft Docs
description: Dowiedz się, jakie atrybuty z SuccessFactors są obsługiwane przez inicjowanie obsługi SuccessFactors-HR
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 6f7497e62be0036c13d5c33fa82301469df16f26
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066189"
---
# <a name="sap-successfactors-attribute-reference"></a>Odwołanie do atrybutu SAP SuccessFactors

## <a name="supported-successfactors-entities-and-attributes"></a>Obsługiwane jednostki i atrybuty SuccessFactors

W poniższej tabeli przechwycono listę atrybutów SuccessFactors obsługiwanych przez następujące dwie aplikacje aprowizacji: 
* [SuccessFactors Active Directory aprowizacji użytkowników](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors do aprowizacji użytkowników usługi Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | Jednostka SuccessFactors                  | SuccessFactors — atrybut     | Typ operacji |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Odczytywanie           |
| 2  | PerPerson                              | personId                     | Odczytywanie           |
| 3  | PerPerson                              | perPersonUuid                | Odczytywanie           |
| 4  | PerPersonal                            | displayName                  | Odczytywanie           |
| 5  | PerPersonal                            | firstName                    | Odczytywanie           |
| 6  | PerPersonal                            | płeć                       | Odczytywanie           |
| 7  | PerPersonal                            | lastName                     | Odczytywanie           |
| 8  | PerPersonal                            | middleName                   | Odczytywanie           |
| 9  | PerPersonal                            | preferowany                | Odczytywanie           |
| 10 | Użytkownik                                   | addressLine1                 | Odczytywanie           |
| 11 | Użytkownik                                   | addressLine2                 | Odczytywanie           |
| 12 | Użytkownik                                   | addressLIne3                 | Odczytywanie           |
| 13 | Użytkownik                                   | businessPhone                | Odczytywanie           |
| 14 | Użytkownik                                   | cellPhone                    | Odczytywanie           |
| 15 | Użytkownik                                   | city                         | Odczytywanie           |
| 16 | Użytkownik                                   | trzeciego                      | Odczytywanie           |
| 17 | Użytkownik                                   | custom01                     | Odczytywanie           |
| 18 | Użytkownik                                   | custom02                     | Odczytywanie           |
| 19 | Użytkownik                                   | custom03                     | Odczytywanie           |
| 20 | Użytkownik                                   | custom04                     | Odczytywanie           |
| 21 | Użytkownik                                   | custom05                     | Odczytywanie           |
| 22 | Użytkownik                                   | custom06                     | Odczytywanie           |
| 23 | Użytkownik                                   | custom07                     | Odczytywanie           |
| 24 | Użytkownik                                   | custom08                     | Odczytywanie           |
| 25 | Użytkownik                                   | custom09                     | Odczytywanie           |
| 26 | Użytkownik                                   | custom10                     | Odczytywanie           |
| 27 | Użytkownik                                   | custom11                     | Odczytywanie           |
| 28 | Użytkownik                                   | custom12                     | Odczytywanie           |
| 29 | Użytkownik                                   | custom13                     | Odczytywanie           |
| 30 | Użytkownik                                   | custom14                     | Odczytywanie           |
| 31 | Użytkownik                                   | empId                        | Odczytywanie           |
| 32 | Użytkownik                                   | homePhone                    | Odczytywanie           |
| 33 | Użytkownik                                   | jobFamily                    | Odczytywanie           |
| 34 | Użytkownik                                   | pseudonim                     | Odczytywanie           |
| 35 | Użytkownik                                   | state                        | Odczytywanie           |
| 36 | Użytkownik                                   | timeZone                     | Odczytywanie           |
| 37 | Użytkownik                                   | nazwa użytkownika                     | Odczytywanie           |
| 38 | Użytkownik                                   | Kod pocztowy                      | Odczytywanie           |
| 39 | PerPhone                               | areaCode                     | Odczytywanie           |
| 40 | PerPhone                               | countryCode                  | Odczytywanie           |
| 41 | PerPhone                               | rozszerzenia                    | Odczytywanie           |
| 42 | PerPhone                               | PhoneNumber                  | Odczytywanie           |
| 43 | PerPhone                               | numer telefonu                    | Odczytywanie           |
| 44 | PerEmail                               | emailAddress                 | Odczyt, zapis    |
| 45 | PerEmail                               | adres e-mail                    | Odczytywanie           |
| 46 | EmpEmployment                          | firstDateWorked              | Odczytywanie           |
| 47 | EmpEmployment                          | lastDateWorked               | Odczytywanie           |
| 48 | EmpEmployment                          | userId                       | Odczytywanie           |
| 49 | EmpEmployment                          | isContingentWorker           | Odczytywanie           |
| 50 | EmpJob                                 | countryOfCompany             | Odczytywanie           |
| 51 | EmpJob                                 | emplStatus                   | Odczytywanie           |
| 52 | EmpJob                                 | endDate                      | Odczytywanie           |
| 53 | EmpJob                                 | startDate                    | Odczytywanie           |
| 54 | EmpJob                                 | Stanowisko                     | Odczytywanie           |
| 55 | EmpJob                                 | położenie                     | Odczytywanie           |
| 65 | EmpJob                                 | customString13               | Odczytywanie           |
| 56 | EmpJob                                 | managerId                    | Odczytywanie           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Odczytywanie           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Odczytywanie           |
| 59 | EmpJob\.firmy                        | company                      | Odczytywanie           |
| 60 | EmpJob\.firmy                        | companyId                    | Odczytywanie           |
| 61 | EmpJob\.firma\.CountryOfRegistration | twoCharCountryCode           | Odczytywanie           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Odczytywanie           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Odczytywanie           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Odczytywanie           |
| 65 | EmpJob\.dział                     | department                   | Odczytywanie           |
| 66 | EmpJob\.dział                     | departmentId                 | Odczytywanie           |
| 67 | EmpJob\.                       | dzielenie                     | Odczytywanie           |
| 68 | EmpJob\.                       | divisionId                   | Odczytywanie           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Odczytywanie           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Odczytywanie           |
| 71 | EmpJob\.lokalizacji                       | Lokalizacja                 | Odczytywanie           |
| 72 | EmpJob\.lokalizacji                       | officeLocationAddress        | Odczytywanie           |
| 73 | EmpJob\.lokalizacji                       | officeLocationCity           | Odczytywanie           |
| 74 | EmpJob\.lokalizacji                       | officeLocationCustomString4  | Odczytywanie           |
| 75 | EmpJob\.lokalizacji                       | officeLocationZipCode        | Odczytywanie           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Odczytywanie           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Odczytywanie           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Odczytywanie           |


## <a name="default-attribute-mapping"></a>Domyślne mapowanie atrybutów

Poniższa tabela zawiera domyślne mapowanie atrybutów między SuccessFactorsmi wymienionymi powyżej i atrybutami AD/Azure AD. W bloku "mapowanie" aplikacji aprowizacji usługi Azure AD można zmodyfikować to mapowanie domyślne w celu uwzględnienia atrybutów z powyższej listy. 

| \# | Jednostka SuccessFactors                  | SuccessFactors — atrybut | Domyślne mapowanie atrybutów AD/Azure AD   | Przetwarzanie uwagi                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | Używane jako pasujący atrybut                                                                   |
| 2  | PerPerson                              | perPersonUuid            | Nie \[zamapowane \- używane jako zakotwiczenie źródła\] | Podczas synchronizacji początkowej usługa aprowizacji łączy personUuid z istniejącym objectGuid\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | Nie dotyczy                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | Nie dotyczy                                                                                           |
| 5  | PerPersonal                            | lastName                 | numery seryjne                                      | Nie dotyczy                                                                                           |
| 6  | Użytkownik                                   | addressLine1             | Adres                           | Nie dotyczy                                                                                           |
| 7  | Użytkownik                                   | city                     | l                                       | Nie dotyczy                                                                                           |
| 8  | Użytkownik                                   | trzeciego                  | co                                      | Nie dotyczy                                                                                           |
| 9  | Użytkownik                                   | state                    | St                                      | Nie dotyczy                                                                                           |
| 10 | Użytkownik                                   | nazwa użytkownika                 | samAccountName                          | Nie dotyczy                                                                                           |
| 11 | Użytkownik                                   | Kod pocztowy                  | postalCode                              | Nie dotyczy                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail (poczta)                                    | Nie dotyczy                                                                                           |
| 13 | EmpJob                                 | Stanowisko                 | title                                   | Nie dotyczy                                                                                           |
| 14 | EmpJob                                 | managerId                | Menedżer                                 | Nie dotyczy                                                                                           |
| 15 | EmpJob\.firma\.CountryOfRegistration | twoCharCountryCode       | c                                       | Nie dotyczy                                                                                           |
| 16 | EmpJob\.dział                     | department               | department                              | Nie dotyczy                                                                                           |
| 17 | EmpJob\.                       | dzielenie                 | company                                 | Nie dotyczy                                                                                           |
| 18 | EmpJob\.lokalizacji                       | officeLocationAddress    | Adres                           | Nie dotyczy                                                                                           |
| 19 | EmpJob\.lokalizacji                       | officeLocationZipCode    | postalCode                              | Nie dotyczy                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Jeśli activeEmploymentsCount = 0, wyłącz account\.                                           |

