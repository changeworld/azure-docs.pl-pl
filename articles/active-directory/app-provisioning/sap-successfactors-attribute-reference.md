---
title: Odwołanie do atrybutów SAP SuccessFactors | Dokumenty firmy Microsoft
description: Dowiedz się, które atrybuty z SuccessFactors są obsługiwane przez successfactors-HR oparte inicjowania obsługi administracyjnej
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 00b16f969525e7b802c008ba247ecba015875689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522360"
---
# <a name="sap-successfactors-attribute-reference"></a>Odwołanie do atrybutu SAP SuccessFactors

## <a name="supported-successfactors-entities-and-attributes"></a>Obsługiwane jednostki i atrybuty SuccessFactors

Poniższa tabela zawiera listę atrybutów SuccessFactors obsługiwanych przez następujące dwie aplikacje inicjowania obsługi administracyjnej: 
* [SuccessFactor do inicjowania obsługi administracyjnej użytkowników usługi Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors do inicjowania obsługi administracyjnej użytkowników usługi Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | Jednostka SuccessFactors                  | Atrybut SuccessFactors     | Typ operacji |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson (osoba perperson)                              | personIdExternal             | Odczyt           |
| 2  | PerPerson (osoba perperson)                              | Personid                     | Odczyt           |
| 3  | PerPerson (osoba perperson)                              | perPersonUuid                | Odczyt           |
| 4  | PerPersonal (PerPersonal)                            | displayName                  | Odczyt           |
| 5  | PerPersonal (PerPersonal)                            | firstName                    | Odczyt           |
| 6  | PerPersonal (PerPersonal)                            | płeć                       | Odczyt           |
| 7  | PerPersonal (PerPersonal)                            | lastName                     | Odczyt           |
| 8  | PerPersonal (PerPersonal)                            | Middlename                   | Odczyt           |
| 9  | PerPersonal (PerPersonal)                            | preferowanana                | Odczyt           |
| 10 | Użytkownik                                   | adresLine1                 | Odczyt           |
| 11 | Użytkownik                                   | Addressline2                 | Odczyt           |
| 12 | Użytkownik                                   | adresLIne3                 | Odczyt           |
| 13 | Użytkownik                                   | telefon firmowy                | Odczyt           |
| 14 | Użytkownik                                   | Telefon                    | Odczyt           |
| 15 | Użytkownik                                   | city                         | Odczyt           |
| 16 | Użytkownik                                   | country                      | Odczyt           |
| 17 | Użytkownik                                   | zwyczaj01                     | Odczyt           |
| 18 | Użytkownik                                   | zwyczaj02                     | Odczyt           |
| 19 | Użytkownik                                   | zwyczaj03                     | Odczyt           |
| 20 | Użytkownik                                   | zwyczaj04                     | Odczyt           |
| 21 | Użytkownik                                   | zwyczaj05                     | Odczyt           |
| 22 | Użytkownik                                   | zwyczaj06                     | Odczyt           |
| 23 | Użytkownik                                   | zwyczaj07                     | Odczyt           |
| 24 | Użytkownik                                   | zwyczaj08                     | Odczyt           |
| 25 | Użytkownik                                   | zwyczaj09                     | Odczyt           |
| 26 | Użytkownik                                   | niestandardowe10                     | Odczyt           |
| 27 | Użytkownik                                   | niestandardowe11                     | Odczyt           |
| 28 | Użytkownik                                   | niestandardowe12                     | Odczyt           |
| 29 | Użytkownik                                   | niestandardowe13                     | Odczyt           |
| 30 | Użytkownik                                   | niestandardowe14                     | Odczyt           |
| 31 | Użytkownik                                   | Empid                        | Odczyt           |
| 32 | Użytkownik                                   | homePhone                    | Odczyt           |
| 33 | Użytkownik                                   | pracaRodzina                    | Odczyt           |
| 34 | Użytkownik                                   | Nick                     | Odczyt           |
| 35 | Użytkownik                                   | state                        | Odczyt           |
| 36 | Użytkownik                                   | timeZone                     | Odczyt           |
| 37 | Użytkownik                                   | nazwa użytkownika                     | Odczyt           |
| 38 | Użytkownik                                   | Zipcode                      | Odczyt           |
| 39 | PerPhone                               | kod obszaru                     | Odczyt           |
| 40 | PerPhone                               | countryCode                  | Odczyt           |
| 41 | PerPhone                               | rozszerzenie                    | Odczyt           |
| 42 | PerPhone                               | phoneNumber                  | Odczyt           |
| 43 | PerPhone                               | phoneType                    | Odczyt           |
| 44 | Poczta Peremail                               | Emailaddress                 | Czytaj, pisz    |
| 45 | Poczta Peremail                               | typ poczty e-mail                    | Odczyt           |
| 46 | EmpEmployment                          | firstDatePracowane              | Odczyt           |
| 47 | EmpEmployment                          | lastDatePracowane               | Odczyt           |
| 48 | EmpEmployment                          | userId                       | Odczyt           |
| 49 | EmpEmployment                          | isContingentWorker           | Odczyt           |
| 50 | EmpJob (empjob)                                 | countryOfCompany             | Odczyt           |
| 51 | EmpJob (empjob)                                 | emplStatus                   | Odczyt           |
| 52 | EmpJob (empjob)                                 | Enddate                      | Odczyt           |
| 53 | EmpJob (empjob)                                 | Startdate                    | Odczyt           |
| 54 | EmpJob (empjob)                                 | jobTitle (Tytuł pracy)                     | Odczyt           |
| 55 | EmpJob (empjob)                                 | pozycja                     | Odczyt           |
| 65 | EmpJob (empjob)                                 | niestandardowychSzeranie13               | Odczyt           |
| 56 | EmpJob (empjob)                                 | Managerid                    | Odczyt           |
| 57 | EmpJob\.Jednostka biznesowa                   | przedsiębiorstwojednostka                 | Odczyt           |
| 58 | EmpJob\.Jednostka biznesowa                   | businessUnitId               | Odczyt           |
| 59 | Firma EmpJob\.                        | company                      | Odczyt           |
| 60 | Firma EmpJob\.                        | companyId (ida)                    | Odczyt           |
| 61 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode           | Odczyt           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Odczyt           |
| 63 | EmpJob\.CostCenter                     | identyfikator kosztówCenterId                 | Odczyt           |
| 64 | EmpJob\.CostCenter                     | kosztówCenterDescription        | Odczyt           |
| 65 | Dział EmpJob\.                     | department                   | Odczyt           |
| 66 | Dział EmpJob\.                     | departmentId                 | Odczyt           |
| 67 | Dywizja EmpJob\.                       | Dywizji                     | Odczyt           |
| 68 | Dywizja EmpJob\.                       | divisionId (ida podziału)                   | Odczyt           |
| 69 | Kod pracy EmpJob\.                        | jobCode (kod pracy)                      | Odczyt           |
| 70 | Kod pracy EmpJob\.                        | jobCodeId                    | Odczyt           |
| 71 | Lokalizacja EmpJob\.                       | Nazwa lokalizacji                 | Odczyt           |
| 72 | Lokalizacja EmpJob\.                       | officeLocationAddress        | Odczyt           |
| 73 | Lokalizacja EmpJob\.                       | officeLocationCity           | Odczyt           |
| 74 | Lokalizacja EmpJob\.                       | officeLocationCustomString4  | Odczyt           |
| 75 | Lokalizacja EmpJob\.                       | officeLocationZipCode        | Odczyt           |
| 76 | EmpJob\.PayGrade                       | stopnie płatności                     | Odczyt           |
| 77 | EmpEmploymentTerminacja               | activeEmploymentsCount       | Odczyt           |
| 78 | EmpEmploymentTerminacja               | latestTerminationDate (Aktualności)        | Odczyt           |


## <a name="default-attribute-mapping"></a>Domyślne mapowanie atrybutów

Poniższa tabela zawiera domyślne mapowanie atrybutów między atrybutami SuccessFactors wymienionymi powyżej i atrybutami AD/Azure AD. W bloku "Mapowanie" aplikacji aprowizacji usługi Azure AD można zmodyfikować to domyślne mapowanie, aby uwzględnić atrybuty z powyższej listy. 

| \# | Jednostka SuccessFactors                  | Atrybut SuccessFactors | Domyślne mapowanie atrybutów usługi AD/Azure AD   | Uwaga przetwarzania                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson (osoba perperson)                              | personIdExternal         | Idpracownika                              | Używany jako pasujący atrybut                                                                   |
| 2  | PerPerson (osoba perperson)                              | perPersonUuid            | \[Nie mapowane \- używane jako źródło kotwicy\] | Podczas synchronizacji początkowej usługa inicjowania obsługi administracyjnej łączy personUuid z istniejącym objectGuid\.  |
| 3  | PerPersonal (PerPersonal)                            | displayName              | displayName                             | Nie dotyczy                                                                                           |
| 4  | PerPersonal (PerPersonal)                            | firstName                | givenName                               | Nie dotyczy                                                                                           |
| 5  | PerPersonal (PerPersonal)                            | lastName                 | sn                                      | Nie dotyczy                                                                                           |
| 6  | Użytkownik                                   | adresLine1             | Streetaddress                           | Nie dotyczy                                                                                           |
| 7  | Użytkownik                                   | city                     | l                                       | Nie dotyczy                                                                                           |
| 8  | Użytkownik                                   | country                  | Co                                      | Nie dotyczy                                                                                           |
| 9  | Użytkownik                                   | state                    | st                                      | Nie dotyczy                                                                                           |
| 10 | Użytkownik                                   | nazwa użytkownika                 | samAccountName                          | Nie dotyczy                                                                                           |
| 11 | Użytkownik                                   | Zipcode                  | Postalcode                              | Nie dotyczy                                                                                           |
| 12 | Poczta Peremail                               | Emailaddress             | mail (poczta)                                    | Nie dotyczy                                                                                           |
| 13 | EmpJob (empjob)                                 | jobTitle (Tytuł pracy)                 | title                                   | Nie dotyczy                                                                                           |
| 14 | EmpJob (empjob)                                 | Managerid                | manager                                 | Nie dotyczy                                                                                           |
| 15 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode       | c                                       | Nie dotyczy                                                                                           |
| 16 | Dział EmpJob\.                     | department               | department                              | Nie dotyczy                                                                                           |
| 17 | Dywizja EmpJob\.                       | Dywizji                 | company                                 | Nie dotyczy                                                                                           |
| 18 | Lokalizacja EmpJob\.                       | officeLocationAddress    | Streetaddress                           | Nie dotyczy                                                                                           |
| 19 | Lokalizacja EmpJob\.                       | officeLocationZipCode    | Postalcode                              | Nie dotyczy                                                                                           |
| 20 | EmpEmploymentTerminacja               | activeEmploymentsCount   | kontoWłasny                          | jeśli activeEmploymentsCount=0, wyłącz konto\.                                           |

