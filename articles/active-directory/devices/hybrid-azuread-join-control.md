---
title: Kontrolowanie dołączenie do hybrydowej usługi Azure AD urządzeń | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kontrolować dołączenie do usługi Azure AD hybrydowej urządzeń w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93afc6f748ca9f464261c59e037a603ab6113bf8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60353112"
---
# <a name="control-the-hybrid-azure-ad-join-of-your-devices"></a>Kontrolowanie dołączania Twoich urządzeń do hybrydowej usługi Azure AD

Dołączenie do hybrydowej usługi Azure Active Directory (Azure AD) to proces automatycznego rejestrowania urządzeń przyłączonych do domeny lokalnej za pomocą usługi Azure AD. Istnieją przypadki, w których nie chcesz, wszystkie swoje urządzenia do zarejestrowania się automatycznie. Ta zasada obowiązuje, na przykład podczas początkowego wdrożenia, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami.

Ten artykuł zawiera wskazówki dotyczące metody kontrolowania urządzeń, dołączenie do usługi Azure AD hybrydowej. 


## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz:

-  [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md)
 
-  [Planowanie implementacji z hybrydowym dołączaniem do usługi Azure Active Directory](hybrid-azuread-join-plan.md)

-  [Konfigurowanie hybrydowej usługi Azure Active Directory join dla domen zarządzanych](hybrid-azuread-join-managed-domains.md) lub [Konfiguruj dołączenie do usługi Azure Active Directory hybrydowej przez domeny federacyjne](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>Kontrolowanie bieżące urządzenia Windows

Dla urządzeń z systemem Windows pulpitu systemu operacyjnego, obsługiwana wersja to Windows Update rozliczenia 10 (wersja 1607) lub nowszej. Najlepszym rozwiązaniem jest uaktualnienie do najnowszej wersji systemu Windows 10.

Logowanie w Windows wszystkie bieżące urządzenia automatycznego rejestrowania w usłudze Azure AD na początku urządzenia lub użytkownika. To zachowanie można kontrolować przy użyciu obiektu zasad grupy (GPO) lub System Center Configuration Manager.

Aby kontrolować bieżące urządzenia Windows, należy: 


1.  **Na wszystkich urządzeniach**: Wyłączenie automatycznej rejestracji urządzeń.
2.  **Do wybranych urządzeń**: Włączanie automatycznej rejestracji urządzeń.

Po upewnieniu się, że wszystko działa zgodnie z oczekiwaniami, możesz ponownie włączyć automatyczną rejestrację urządzeń dla wszystkich urządzeń.



### <a name="group-policy-object"></a>Obiekt zasad grupy 

Przez wdrożenie następujących zasad grupy, można kontrolować zachowanie rejestracji urządzenia z Twoich urządzeń: **Rejestrowanie komputerów przyłączonych do domeny jako urządzenia**.

Aby ustawić obiekt zasad grupy:

1.  Otwórz **Menedżera serwera**, a następnie przejdź do **narzędzia** > **Zarządzanie zasadami grupy**.

2.  Przejdź do węzła domeny, która odnosi się do domeny, na którym chcesz wyłączyć lub włączyć automatyczną rejestrację.

3.  Kliknij prawym przyciskiem myszy **obiekty zasad grupy**, a następnie wybierz pozycję **New**.

4.  Wprowadź nazwę (na przykład **dołączenie do hybrydowej usługi Azure AD**) dla obiektu zasad grupy. 

5.  Kliknij przycisk **OK**.

6.  Kliknij prawym przyciskiem myszy nowy obiekt zasad grupy, a następnie wybierz **Edytuj**.

7.  Przejdź do **konfiguracji komputera** > **zasady** > **Szablony administracyjne** > **Windows Składniki** > **rejestracja urządzeń w usłudze**. 

8.  Kliknij prawym przyciskiem myszy **zarejestrować komputery przyłączone do domeny jako urządzenia**, a następnie wybierz pozycję **Edytuj**.

    > [!NOTE] 
    > Ten szablon zasad grupy została zmieniona z wcześniejszych wersjach konsoli zarządzania zasadami grupy. Jeśli używasz starszej wersji konsoli, przejdź do strony **konfiguracji komputera** > **zasady** > **Szablony administracyjne**  >  **Składników Windows** > **rejestracja urządzeń w usłudze** > **rejestr domeny przyłączyć komputer jako urządzenie**. 

9.  Wybierz jedną z następujących ustawień, a następnie wybierz **Zastosuj**:

    - **Wyłączone**: Aby zapobiec automatycznej rejestracji urządzeń.
    - **Włączone**: Aby włączyć automatycznej rejestracji urządzeń.

10. Kliknij przycisk **OK**.

Należy połączyć obiekt zasad grupy z wybraną lokalizację. Na przykład aby ustawić te zasady dla wszystkich przyłączonych do domeny bieżącego urządzeń w Twojej organizacji, należy połączyć obiekt zasad grupy do domeny. Przeprowadzenie wdrożenia kontrolowanego, należy ustawić te zasady na przyłączonym do domeny Windows bieżące urządzenia, które należą do jednostki organizacyjnej lub grupie zabezpieczeń.

### <a name="configuration-manager-controlled-deployment"></a>Wdrożenie kontrolowane w programie Configuration Manager 

Można kontrolować zachowanie rejestracji urządzenia bieżącego urządzeń, konfigurując następującego ustawienia klienta: **Automatycznego rejestrowania nowych urządzeń przyłączonych do domeny systemu Windows 10 w usłudze Azure Active Directory**.

Aby skonfigurować ustawienia klienta:

1.  Otwórz **programu Configuration Manager**, wybierz opcję **administracji**, a następnie przejdź do **ustawienia klienta**.

2.  Otwórz właściwości **domyślne ustawienia klienta** i wybierz **usług w chmurze**.

3.  W obszarze **ustawienia urządzenia**, wybierz jedną z następujących ustawień **automatycznego rejestrowania nowych urządzeń przyłączonych do domeny systemu Windows 10 w usłudze Azure Active Directory**:

    - **Nie**: Aby zapobiec automatycznej rejestracji urządzeń.
    - **Tak**: Aby włączyć automatycznej rejestracji urządzeń.

4.  Kliknij przycisk **OK**.

Należy połączyć to ustawienie klienta z wybraną lokalizację. Na przykład aby skonfigurować to ustawienie dla wszystkich urządzeń bieżącego Windows w organizacji klienta, należy połączyć ustawienia domeny klienta. Przeprowadzenie wdrożenia kontrolowanego, można skonfigurować ustawienie klienta umożliwiające Windows przyłączone do domeny bieżącego urządzenia, które należą do jednostki organizacyjnej lub grupie zabezpieczeń.

> [!Important]
> Mimo że tej konfiguracji zajmuje się istniejących urządzeń systemu Windows 10 przyłączonych do domeny, urządzenia, które są nowo dołączenia do domeny może nadal próbować wykonać dołączenie do hybrydowej usługi Azure AD, ze względu na potencjalne opóźnienia w stosowanie zasad grupy lub Ustawienia programu Configuration Manager na urządzeniach. 
>
> Aby tego uniknąć, zalecamy utworzenie nowego obrazu Sysprep (jako przykład użyto metody inicjowania obsługi administracyjnej). Należy go utworzyć za pomocą urządzenia, który został wcześniej hybrydowe przyłączone do usługi Azure AD i że ma już zasad grupy ustawienie lub zastosowane ustawienia klienta programu Configuration Manager. Należy również użyć nowego obrazu do inicjowania obsługi nowych komputerach, które dołączenia do domeny w organizacji. 

## <a name="control-windows-down-level-devices"></a>Kontrolowanie urządzeń z systemem Windows niższego poziomu

Aby zarejestrować urządzenia niskiego poziomu Windows, musisz pobrać i zainstalować pakiet Instalatora Windows (msi) z Centrum pobierania na [Microsoft dołączania komputerów do systemu Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) strony.

Pakiet można wdrożyć za pomocą to system dystrybucji oprogramowania, takie jak [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Pakiet obsługuje opcje standardowej instalacji dyskretnej, za pomocą parametru cichy. Current branch programu Configuration Manager zapewnia korzyści w porównaniu ze starszymi wersjami, takich jak możliwość śledzenia rejestracji zakończonych.

Instalator jest utworzenie zaplanowanego zadania w systemie, który jest uruchamiany w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do Windows. Zadanie dyskretnie łączy urządzenie z usługą Azure AD przy użyciu poświadczeń użytkownika, po uwierzytelnieniu w usłudze Azure AD.

Aby kontrolować rejestracji urządzeń, należy wdrożyć pakiet Instalatora Windows tylko dla wybranej grupy systemu Windows niższego poziomu urządzeń. Jeśli sprawdzono, że wszystko działa zgodnie z oczekiwaniami, możesz wdrożyć pakiet do wszystkich urządzeń niskiego poziomu.


## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md)



