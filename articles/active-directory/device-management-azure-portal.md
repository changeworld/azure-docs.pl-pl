---
title: Zarządzanie urządzeniami przy użyciu portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać portalu Azure do zarządzania urządzeniami.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: ddce312d6299a7749f1a40a83f67ce3c26876591
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714004"
---
# <a name="managing-devices-using-the-azure-portal"></a>Zarządzanie urządzeniami przy użyciu portalu Azure


Z zarządzania urządzeniami w usłudze Azure Active Directory (Azure AD) można zapewnić, że użytkownicy uzyskują dostęp do zasobów z urządzeń, które spełniają standardy zabezpieczeń i zgodności. 

W tym artykule:

- Przyjęto założenie, że czytelnik zna [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](device-management-introduction.md)

- Dostarcza informacji na temat zarządzania urządzeniami przy użyciu portalu Azure

## <a name="manage-devices"></a>Zarządzanie urządzeniami 

Azure portal udostępnia centralne miejsce do zarządzania urządzeniami. Możesz przejść do tego miejsca firmy przy użyciu [bezpośredniego łącza](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) lub ręcznie wykonaj następujące czynności:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako administrator.

2. Lewy pasek nawigacyjny, wybierz polecenie **usługi Active Directory**.

    ![Konfiguruj ustawienia urządzenia](./media/device-management-azure-portal/01.png)

3. W **Zarządzaj** kliknij **urządzeń**.

    ![Konfiguruj ustawienia urządzenia](./media/device-management-azure-portal/11.png)
 
**Urządzeń** umożliwia:

- Konfigurowanie ustawień zarządzania urządzeniami

- Znajdź urządzenia

- Wykonywanie zadań zarządzania urządzeniami

- Przejrzyj zarządzania urządzeniami, związane z dziennika inspekcji  
  

## <a name="configure-device-settings"></a>Konfiguruj ustawienia urządzenia

Do zarządzania urządzeniami przy użyciu portalu Azure, urządzenia muszą być albo [zarejestrowana lub przyłączony](device-management-introduction.md#getting-devices-under-the-control-of-azure-ad) do usługi Azure AD. Jako administrator Aby precyzyjnie zdefiniować proces rejestracji i dołączenie urządzeń przez skonfigurowanie ustawień urządzenia. 

![Konfiguruj ustawienia urządzenia](./media/device-management-azure-portal/22.png)

Strona ustawień urządzenia można skonfigurować:

![Zarządzanie urządzeń w usłudze Intune](./media/device-management-azure-portal/21.png)


- **Użytkownicy mogą dołączać urządzenia do usługi Azure AD** — to ustawienie umożliwia wybranie użytkowników, którzy mogą [dołączać urządzenia](device-management-introduction.md#azure-ad-joined-devices) do usługi Azure AD. Wartość domyślna to **wszystkich**.

- **Urządzeniach przyłączonych do kolejnych administratorów lokalnych na usługi Azure AD** — można wybrać użytkowników, którzy mają prawa administratora lokalnego na urządzeniu. Dodane w tym miejscu użytkownicy są dodawani do *Administratorzy urządzenia* roli w usłudze Azure AD. Administratorzy globalni w usłudze Azure AD i właścicielom urządzeń mają prawa administratora lokalnego domyślnie. Ta opcja jest dostępna za pośrednictwem produktów, takich jak Azure AD Premium lub Enterprise Mobility Suite (EMS) możliwości wersji premium. 

- **Użytkownicy mogą zarejestrować swoje urządzenia w usłudze Azure AD** — należy skonfigurować to ustawienie, aby zezwolić urządzeniom na można [zarejestrowany](device-management-introduction.md#azure-ad-registered-devices) z usługą Azure AD. W przypadku wybrania **Brak**, urządzenia są niedozwolone do rejestru, jeśli nie są one usługi Azure AD przyłączone lub hybrydowego przyłączonych do usługi Azure AD. Rejestrowanie w usłudze Microsoft Intune lub zarządzania urządzeniami przenośnymi (MDM) dla usługi Office 365 wymaga rejestracji. Jeśli skonfigurowano którąś z tych usług **wszystkie** jest zaznaczone i **Brak** nie jest dostępna.

- **Wymagaj uwierzytelniania wieloskładnikowego urządzeń** — możesz wybrać, czy użytkownicy muszą zapewnić drugi składnik uwierzytelniania w celu [sprzężenia](device-management-introduction.md#azure-ad-joined-devices) swoich urządzeń do usługi Azure AD. Wartość domyślna to **nr**. Firma Microsoft zaleca, wymagając uwierzytelniania wieloskładnikowego podczas rejestrowania urządzenia. Przed włączeniem uwierzytelniania wieloskładnikowego dla tej usługi, musisz zapewnić, że skonfigurowano uwierzytelnianie wieloskładnikowe dla użytkowników, które rejestrują swoje urządzenia. Aby uzyskać więcej informacji dotyczących usług różnych uwierzytelnianie wieloskładnikowe platformy Azure, zobacz [wprowadzenie do korzystania z usługi Azure Multi-Factor authentication](authentication/concept-mfa-whichversion.md). 

- **Maksymalna liczba urządzeń** — to ustawienie umożliwia wybranie maksymalną liczbę urządzeń, które użytkownik może mieć w usłudze Azure AD. Jeśli użytkownik osiągnie ten limit przydziału, nie są będzie można dodać dodatkowych urządzeń dopóki jedna lub więcej istniejące urządzenia zostaną usunięte. Oferta urządzenia jest liczony dla wszystkich urządzeń, które są usługi Azure AD przyłączone lub usługi Azure AD, w obecnie zarejestrowany. Wartość domyślna to **20**.

- **Użytkownicy mogą synchronizować ustawienia i dane aplikacji na urządzeniach** — domyślnie to ustawienie ma wartość **Brak**. Wybranie określonych użytkowników lub grup lub wszystkich umożliwia użytkownika ustawień i danych aplikacji można zsynchronizować na ich urządzeniach z systemem Windows 10. Dowiedz się więcej informacji na temat sposobu synchronizacji działa w systemie Windows 10.
Ta opcja jest dostępna za pośrednictwem produktów, takich jak Azure AD Premium lub Enterprise Mobility Suite (EMS) możliwości premium.
 




## <a name="locate-devices"></a>Znajdź urządzenia

Dostępne są dwie opcje można znaleźć urządzenia zarejestrowane i są przyłączone do:

- **Wszystkie urządzenia** w **Zarządzaj** sekcji **urządzeń** strony  

    ![Wszystkie urządzenia](./media/device-management-azure-portal/41.png)


- **Urządzenia** w **Zarządzaj** sekcji **użytkownika** strony
 
    ![Wszystkie urządzenia](./media/device-management-azure-portal/43.png)



Z obu opcji, aby przejść do widoku który:


- Umożliwia wyszukiwanie urządzeń przy użyciu nazwy wyświetlanej jako filtr.

- Udostępnia szczegółowy przegląd urządzeń zarejestrowanych i połączone

- Służy do wykonywania typowych zadań zarządzania dla urządzenia
   

![Wszystkie urządzenia](./media/device-management-azure-portal/51.png)

Dla niektórych urządzeń z systemem iOS zawierające apostrofów nazwy urządzenia mogą za pomocą różnych znaki wyglądają jak apostrofy. Dlatego wyszukiwanie takich urządzeń jest nieco trudnych — Jeśli nie widzisz wyniki wyszukiwania prawidłowo, upewnij się, że ciąg wyszukiwania zawiera pasującego apostrof.

## <a name="device-management-tasks"></a>Zadania dotyczące zarządzania urządzeniami

Jako administrator można zarządzać urządzeniami zarejestrowane lub połączone. Ta sekcja zawiera informacje o typowych zadań zarządzania dla urządzenia.


### <a name="manage-an-intune-device"></a>Zarządzanie urządzeń w usłudze Intune

Jeśli jesteś administratorem usługi Intune, możesz zarządzać urządzeniami oznaczona jako **Microsoft Intune**. Administrator może zobaczyć dodatkowe urządzenia 

![Zarządzanie urządzeń w usłudze Intune](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>Włączanie / wyłączanie urządzeń usługi Azure AD

Włącz / Wyłącz urządzenie, dostępne są dwie opcje:

- Menu zadania ("...") na **wszystkie urządzenia** strony

    ![Zarządzanie urządzeń w usłudze Intune](./media/device-management-azure-portal/71.png)

- Na pasku narzędzi u **urządzeń** strony

    ![Zarządzanie urządzeń w usłudze Intune](./media/device-management-azure-portal/32.png)


**Uwagi:**

- Musisz być administratorem globalnym w usłudze Azure AD, aby włączyć / wyłączyć urządzenie. 
- Wyłączenie urządzenia zapobiega urządzenia podczas uzyskiwania dostępu do zasobów usługi Azure AD. 



### <a name="delete-an-azure-ad-device"></a>Usuwanie urządzenia z systemem Azure AD

Aby usunąć urządzenie, masz dwie opcje:

- Menu zadania ("...") na **wszystkie urządzenia** strony

    ![Zarządzanie urządzeń w usłudze Intune](./media/device-management-azure-portal/72.png)

- Na pasku narzędzi u **urządzeń** strony

    ![Usuwanie urządzenia](./media/device-management-azure-portal/34.png)


**Uwagi:**

- Musisz być administratorem globalnym w usłudze Azure AD, aby usunąć urządzenia.  

- Usuwanie urządzenia:
 
    - Urządzenie uniemożliwia dostęp do zasobów usługi Azure AD. 

    - Usuwa wszystkie szczegóły, które są dołączone do urządzenia, na przykład, klucze funkcji BitLocker dla urządzeń z systemem Windows.  

    - Reprezentuje nieodwracalny działania i nie jest zalecane, chyba że jest to wymagane.

Jeśli urządzenie jest zarządzane przez inny urząd zarządzania (na przykład Microsoft Intune), upewnij się, że urządzenie zostało wyczyszczone / wycofane przed usunięciem urządzenia w usłudze Azure AD.

 


### <a name="view-or-copy-device-id"></a>Wyświetl lub skopiuj identyfikator urządzenia

Identyfikator urządzenia służy do sprawdzenia szczegóły identyfikator urządzenia na urządzeniu lub podczas rozwiązywania problemów przy użyciu programu PowerShell. Aby uzyskać dostęp do opcji kopię, kliknij urządzenie.

![Wyświetl identyfikator urządzenia](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>Wyświetl lub skopiować klucze funkcji BitLocker

Można wyświetlać i kopiowanie kluczy funkcji BitLocker, aby ułatwić użytkownikom na odzyskiwanie ich zaszyfrowanego dysku. Klucze te są dostępne tylko dla urządzeń z systemem Windows, które są szyfrowane i ich kluczy przechowywanych w usłudze Azure AD. Możesz skopiować te klucze podczas uzyskiwania dostępu do szczegółów urządzenia.
 
![Wyświetl klucze funkcji BitLocker](./media/device-management-azure-portal/36.png)

Aby wyświetlić lub skopiować klucze funkcji BitLocker, należy być właściciela urządzenia lub użytkownika, który ma co najmniej jedną z następujących ról przypisane:

- Administratorzy globalni
- Administratorzy pomoc techniczna
- Administratorzy zabezpieczeń
- Czytniki zabezpieczeń
- Administratorzy usługi Intune


## <a name="audit-logs"></a>Dzienniki inspekcji


Urządzenie działań są dostępne Dzienniki aktywności. Obejmuje to działania wyzwalane przez usługę rejestracji urządzeń i użytkowników:

- Tworzenie urządzenia i dodawanie właścicieli / użytkowników na urządzeniu

- Zmiany w ustawieniach urządzenia

- Urządzenie operacji, takich jak usuwanie lub aktualizowanie urządzenia
 
Punkt wejścia do inspekcji danych jest **dzienniki inspekcji** w **działania** sekcji **urządzeń** strony.

![Dzienniki inspekcji](./media/device-management-azure-portal/61.png)


Dziennik inspekcji zawiera domyślny widok listy, który pokazuje:

- Data i godzina wystąpienia

- obiekty docelowe

- Inicjator / aktora (który) działania

- działanie (co)

![Dzienniki inspekcji](./media/device-management-azure-portal/63.png)

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.
 
![Dzienniki inspekcji](./media/device-management-azure-portal/64.png)


Aby zawęzić zgłaszane dane do odpowiedniego poziomu, możesz odfiltrować dane inspekcji przy użyciu następujących pól:

- Kategoria
- Typ zasobu działania
- Działanie
- Zakres dat
- Środowisko docelowe
- Zainicjowane przez (aktor)

Oprócz filtrów możesz wyszukać wybrane wpisy.

![Dzienniki inspekcji](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](device-management-introduction.md)



