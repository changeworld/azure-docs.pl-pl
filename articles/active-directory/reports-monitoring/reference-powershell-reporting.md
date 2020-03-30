---
title: Polecenia cmdlet usługi Azure AD PowerShell do raportowania | Dokumenty firmy Microsoft
description: Odwołanie do poleceń cmdlet programu Azure AD PowerShell do raportowania.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/12/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2192c472e00d123780ec6bc5574e7b9fe326258b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495309"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Polecenia cmdlet programu PowerShell usługi Azure AD do raportowania

> [!NOTE] 
> Te polecenia cmdlet programu Powershell obecnie działają tylko z modułem [usługi Azure AD Preview.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) Należy pamiętać, że moduł podglądu nie jest sugerowany do użytku produkcyjnego. 

Aby zainstalować publiczną wersję zapoznawczą, należy użyć następujących czynności. 

```powershell
Install-module AzureADPreview
```
Aby uzyskać więcej infromation na temat łączenia się z usługą Azure AD za pomocą programu PowerShell, zobacz artykuł [Azure AD Powershell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).  

Dzięki raportom usługi Azure Active Directory (Azure AD) można uzyskać szczegółowe informacje na temat działań wokół wszystkich operacji zapisu w twoim kierunku (dzienniki inspekcji) i dane uwierzytelniania (dzienniki logowania). Chociaż informacje są dostępne przy użyciu interfejsu API programu MS Graph, teraz można pobrać te same dane przy użyciu poleceń cmdlet programu Azure AD PowerShell do raportowania.

W tym artykule przedstawiono omówienie poleceń cmdlet programu PowerShell, które mają być używane do dzienników inspekcji i dzienników logowania.

## <a name="audit-logs"></a>Dzienniki inspekcji

[Dzienniki inspekcji](concept-audit-logs.md) zapewniają identyfikowalność za pośrednictwem dzienników dla wszystkich zmian wykonywanych przez różne funkcje w usłudze Azure AD. Przykłady dzienników inspekcji obejmują zmiany wprowadzone do wszystkich zasobów w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

Dostęp do dzienników inspekcji można uzyskać przy użyciu polecenia cmdlet "Get-AzureADAuditDirectoryLogs.You get access to the audit logs using the 'Get-AzureADAuditDirectoryLogs cmdlet.


| Scenariusz                      | Polecenie programu PowerShell |
| :--                           | :--                |
| Nazwa wyświetlana aplikacji      | Get-AzureADAuditDirectoryLogs -Filter "initiatedBy/app/displayName eq 'Azure AD Cloud Sync'" |
| Kategoria                      | Get-AzureADAuditDirectoryLogs -Filter "kategoria eq 'Zarządzanie aplikacjami'" |
| Data działania Godzina            | Get-AzureADAuditDirectoryLogs -Filter "activityDateTime gt 2019-04-18" |
| Wszystkie powyższe              | Get-AzureADAuditDirectoryLogs -Filter "initiatedBy/app/displayName eq 'Azure AD Cloud Sync' i kategoria eq 'Zarządzanie aplikacjami' i activityDateTime gt 2019-04-18"|


Na poniższej ilustracji przedstawiono przykład tego polecenia. 

![Przycisk "Podsumowanie danych"](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Dzienniki logowania

Dzienniki [logowania](concept-sign-ins.md) zawierają informacje o użyciu zarządzanych aplikacji i działań logowania użytkownika.

Otrzymasz dostęp do dzienników logowania przy użyciu polecenia cmdlet Get-AzureADAuditSignInLogs.


| Scenariusz                      | Polecenie programu PowerShell |
| :--                           | :--                |
| Nazwa wyświetlana użytkownika             | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins'" |
| Utwórz godzinę daty              | Get-AzureADAuditSignInLogs -Filter "createdDateTime gt 2019-04-18T17:30:00.0Z" (Wszystko od 17:30 w dniu 18.04.18) |
| Stan                        | Get-AzureADAuditSignInLogs -Filter "status/errorCode eq 50105" |
| Nazwa wyświetlana aplikacji      | Get-AzureADAuditSignInLogs -Filter "appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |
| Wszystkie powyższe              | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins' i status/errorCode ne 0 i appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |


Na poniższej ilustracji przedstawiono przykład tego polecenia. 

![Przycisk "Podsumowanie danych"](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Następne kroki

- [Omówienie raportów usługi Azure AD](overview-reports.md).
- [Raport dzienników inspekcji](concept-audit-logs.md). 
- [Programowy dostęp do raportów usługi Azure AD](concept-reporting-api.md)
