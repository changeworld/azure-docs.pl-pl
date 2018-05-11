---
title: GDPR w serwer proxy aplikacji usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o GDPR w serwer proxy aplikacji usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a3df15743b4918d72fac5f8769a2d3ee721a452f
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="gdpr-in-the-azure-active-directory-application-proxy"></a>GDPR w serwer proxy aplikacji usługi Azure Active Directory  

Serwer Proxy aplikacji usługi Azure Active Directory (Azure AD) jest zgodny z interfejsem GDPR wraz z innych usług firmy Microsoft i funkcje. Aby dowiedzieć się więcej o obsłudze GDPR firmy Microsoft, zobacz [postanowienia licencyjne i dokumentacji](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Ponieważ ta funkcja zawiera łączniki na komputerach, istnieje kilka zdarzeń, które należy monitorować pozostanie GDPR zgodne. Serwer Proxy aplikacji tworzy następujące typy dziennika, które mogą zawierać EUII:

- Dzienniki zdarzeń łącznika
- Dzienniki zdarzeń systemu Windows

Istnieją dwa sposoby pozostać GDPR zgodnych:

- Usuń i wyeksportować żądania, gdy występują one

- Wyłącz rejestrowanie

W przypadku:

- Informacje na temat konfigurowania przechowywania danych dzienników zdarzeń systemu Windows, temacie [ustawienia dzienników zdarzeń](https://technet.microsoft.com/library/cc952132.aspx). 
- Ogólne informacje w dzienniku zdarzeń systemu Windows, temacie [dziennika zdarzeń systemu Windows przy użyciu](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).


Można znaleźć w dziennikach zdarzeń connect w `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. Poniższe sekcje zawierają procedury powiązanych dzienników zdarzeń łącznika. Należy wykonać następujące kroki na wszystkich komputerach łącznika.
 

## <a name="processing-requests"></a>Przetwarzanie żądań

Istnieją trzy różne typy żądań, które odpowiadają za: 

- Usuwanie
- Widok
- Eksportowanie
 
Widok procesu / Eksportuj żądań, należy przejść przez każdy z plików dziennika, aby wyszukać powiązane wpisy zarejestrowane. 

Ponieważ dzienniki są plikami tekstowymi, można wyszukiwać według, na przykład za pomocą `findstr` polecenie, aby znaleźć wpisy związane z użytkownikiem. Wyszukaj następujące pola, ponieważ mogą one być w dziennikach: 

- UserId
- Wpisz nazwę użytkownika skonfigurowane dla dowolnej aplikacji za pomocą ograniczone delegowanie protokołu Kerberos:
    - Główna nazwa użytkownika
    - Główna nazwa użytkownika lokalnego
    - Część nazwy użytkownika głównej nazwy użytkownika
    - Część nazwy użytkownika głównej nazwy użytkownika lokalnego
    - Nazwa lokalnego konta SAM 

 
Następnie można zbierać te pola i udostępniać je z użytkownikiem.
Przetwarzanie żądań delete, należy usunąć odpowiednie dzienników. Można ponownie uruchomić usługę łącznika (Microsoft Azure AD łącznika serwera Proxy aplikacji), aby wygenerować nowy plik dziennika. Nowy plik dziennika umożliwia usunięcie starych plików dziennika. Można następnie postępuj zgodnie z procesem widoku / Eksportuj do Znajdź wszystkie dzienniki odpowiednich i selektywnie usunąć te pola lub pliki. Możesz również zawsze usunąć wszystkie stare pliki dziennika, jeśli użytkownik nie są już potrzebne.


## <a name="turn-off-connector-logs"></a>Wyłącz łącznik dzienników

Aby wyłączyć łącznik dzienniki, musisz dostosować `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` przez usunięcie wyróżniony wiersz: 


![Konfigurowanie](./media/active-directory-application-proxy-gdpr/01.png)


## <a name="next-steps"></a>Kolejne kroki

Omówienie serwera proxy aplikacji usługi Azure AD, zobacz [jak zapewnić bezpieczny zdalny dostęp do aplikacji lokalnych](manage-apps/application-proxy.md).

