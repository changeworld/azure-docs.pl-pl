---
title: Jak zarządzać urządzeniami za pomocą witryny Azure portal | Dokumenty firmy Microsoft
description: Dowiedz się, jak zarządzać urządzeniami za pomocą witryny Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09de5911ca0946bfcbcb77d1ad4131c8feac9f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262245"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Zarządzanie tożsamościami urządzeń za pomocą witryny Azure portal

Dzięki zarządzaniu tożsamością urządzeń w usłudze Azure Active Directory (Azure AD) można zapewnić, że użytkownicy uzyskują dostęp do zasobów z urządzeń spełniających twoje standardy zabezpieczeń i zgodności.

W tym artykule:

- Przyjęto założenie, że użytkownik jest zaznajomiony z [wprowadzeniem do zarządzania tożsamościami urządzeń w usłudze Azure Active Directory](overview.md)
- Zawiera informacje dotyczące zarządzania tożsamościami urządzeń przy użyciu portalu usługi Azure AD

## <a name="manage-device-identities"></a>Zarządzanie tożsamościami urządzeń

Portal usługi Azure AD zapewnia centralne miejsce do zarządzania tożsamościami urządzeń. Możesz dostać się do tego miejsca za pomocą [bezpośredniego linku](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) lub:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Przejdź do **usługi Azure Active Directory** > **Devices**.

Strona **Urządzenia** umożliwia:

- Konfigurowanie ustawień urządzenia
- Lokalizowanie urządzeń
- Wykonywanie zadań zarządzania tożsamościami urządzeń
- Przeglądanie dzienników inspekcji związanych z urządzeniem  
  
## <a name="configure-device-settings"></a>Konfiguruj ustawienia urządzenia

Aby zarządzać tożsamościami urządzeń przy użyciu portalu usługi Azure AD, twoje urządzenia muszą być [zarejestrowane lub przyłączone](overview.md) do usługi Azure AD. Jako administrator możesz dostosować proces rejestracji i łączenia urządzeń, konfigurując ustawienia urządzenia.

Strona ustawień urządzenia umożliwia skonfigurowanie ustawień związanych z tożsamościami urządzeń:

![Zarządzanie urządzeniem usługi Intune](./media/device-management-azure-portal/21.png)

- **Użytkownicy mogą dołączać urządzenia do usługi Azure AD** — to ustawienie umożliwia wybranie użytkowników, którzy mogą rejestrować swoje urządzenia jako urządzenia przyłączone do usługi Azure AD. Wartością domyślną jest **wszystkich**.

> [!NOTE]
> **Użytkownicy mogą dołączyć urządzenia do usługi Azure AD** ustawienie ma zastosowanie tylko do sprzężenia usługi Azure AD w systemie Windows 10.

- **Dodatkowi administratorzy lokalni na urządzeniach przyłączonych do usługi Azure AD** — można wybrać użytkowników, którym przyznano prawa administratora lokalnego na urządzeniu. Użytkownicy dodana w tym miejscu są dodawane do roli *Administratorzy urządzeń* w usłudze Azure AD. Administratorzy globalni w usłudze Azure AD i właściciele urządzeń są domyślnie przyznawane lokalne prawa administratora. Ta opcja jest dostępna w wersji premium za pośrednictwem produktów, takich jak Azure AD Premium lub Enterprise Mobility Suite (EMS).
- **Użytkownicy mogą rejestrować swoje urządzenia za pomocą usługi Azure AD** — należy skonfigurować to ustawienie, aby umożliwić urządzenia z systemem Windows 10 personal, iOS, Android i macOs, które mają być zarejestrowane w usłudze Azure AD. Jeśli wybierzesz **Brak**, urządzenia nie mogą rejestrować się w usłudze Azure AD. Rejestracja w usłudze Microsoft Intune lub zarządzanie urządzeniami przenośnymi (MDM) dla usługi Office 365 wymaga rejestracji. Jeśli skonfigurowano jedną z tych usług, **all** jest zaznaczone i **NONE** nie jest dostępna.
- **Wymagaj wieloskładnikowego auth do łączenia urządzeń** — można wybrać, czy użytkownicy są zobowiązani do zapewnienia dodatkowego czynnika uwierzytelniania, aby dołączyć do urządzenia do usługi Azure AD. Wartość domyślna to **Nie**. Zaleca się wymaganie uwierzytelniania wieloskładnikowego podczas rejestrowania urządzenia. Przed włączeniem uwierzytelniania wieloskładnikowego dla tej usługi należy upewnić się, że uwierzytelnianie wieloskładnikowe jest skonfigurowane dla użytkowników, którzy rejestrują swoje urządzenia. Aby uzyskać więcej informacji na temat różnych usług uwierzytelniania wieloskładnikowego platformy Azure, zobacz [wprowadzenie do uwierzytelniania wieloskładnikowego platformy Azure.](../authentication/concept-mfa-whichversion.md) 

> [!NOTE]
> **Wymagaj wieloskładnikowego auth do przyłączenia się do urządzeń** ustawienie dotyczy urządzeń, które są albo usługi Azure AD przyłączone lub usługi Azure AD zarejestrowanych. To ustawienie nie ma zastosowania do hybrydowych urządzeń przyłączonych do usługi Azure AD.

- **Maksymalna liczba urządzeń** — to ustawienie umożliwia wybranie maksymalnej liczby urządzeń przyłączonych do usługi Azure AD lub zarejestrowanych w usłudze Azure AD, które użytkownik może mieć w usłudze Azure AD. Jeśli użytkownik osiągnie ten przydział, nie będzie mógł dodać dodatkowych urządzeń, dopóki nie usunie się co najmniej jednego z istniejących urządzeń. Wartość domyślna to **20**.

> [!NOTE]
> **Maksymalna liczba urządzeń** ustawienie ma zastosowanie do urządzeń, które są przyłączone do usługi Azure AD lub usługi Azure AD zarejestrowanych. To ustawienie nie ma zastosowania do hybrydowych urządzeń przyłączonych do usługi Azure AD.

- **Użytkownicy mogą synchronizować ustawienia i dane aplikacji na różnych urządzeniach** — domyślnie to ustawienie jest ustawione na **BRAK**. Wybranie określonych użytkowników lub grup lub ALL umożliwia synchronizację ustawień i danych aplikacji użytkownika na urządzeniach z systemem Windows 10. Dowiedz się więcej o tym, jak działa synchronizacja w systemie Windows 10.
Ta opcja jest dostępna w wersji premium za pośrednictwem produktów, takich jak Azure AD Premium lub Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Lokalizowanie urządzeń

Dostępne są dwie opcje lokalizowania zarejestrowanych i przyłączonych urządzeń:

- **Wszystkie urządzenia** w sekcji **Zarządzanie** na stronie **Urządzenia**  
- **Urządzenia** w sekcji **Zarządzanie** na stronie **Użytkownika**

W obu opcjach można uzyskać widok, który:

- Umożliwia wyszukiwanie urządzeń przy użyciu nazwy wyświetlanej lub identyfikatora urządzenia jako filtru.
- Zapewnia szczegółowy przegląd zarejestrowanych i przyłączonych urządzeń
- Umożliwia wykonywanie typowych zadań związanych z zarządzaniem urządzeniami

![Wszystkie urządzenia](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Jeśli widzisz urządzenie, które jest "Hybrid Azure AD połączone" ze stanem "Oczekujące" w kolumnie REGISTERED, wskazuje, że urządzenie zostało zsynchronizowane z usługi Azure AD connect i oczekuje na zakończenie rejestracji od klienta. Dowiedz się więcej o [planowaniu implementacji dołączania do usługi Hybrid Azure AD.](hybrid-azuread-join-plan.md) Dodatkowe informacje można znaleźć w artykule, [Urządzenia często zadawane pytania](faq.md).
>
>   ![Urządzenia oczekujące](./media/device-management-azure-portal/75.png)
>
>* W przypadku niektórych urządzeń z systemem iOS nazwy urządzeń zawierające apostrofy mogą potencjalnie używać różnych znaków, które wyglądają jak apostrofy. Więc wyszukiwanie takich urządzeń jest trochę trudne - jeśli nie widzisz poprawnie wyników wyszukiwania, upewnij się, że ciąg wyszukiwania zawiera pasujący znak apostrofu.

## <a name="device-identity-management-tasks"></a>Zadania zarządzania tożsamościami urządzeń

Jako administrator globalny lub administrator urządzeń w chmurze możesz zarządzać zarejestrowanymi lub przyłączanymi urządzeniami. Administratorzy usługi intune mogą:

- Aktualizuj urządzenia — przykłady to codzienne operacje, takie jak włączanie/wyłączanie urządzeń
- Usuwanie urządzeń — gdy urządzenie jest wycofywane i powinno zostać usunięte w usłudze Azure AD

Ta sekcja zawiera informacje o typowych zadaniach zarządzania tożsamościami urządzeń.

### <a name="manage-an-intune-device"></a>Zarządzanie urządzeniem usługi Intune

Jeśli jesteś administratorem usługi Intune, możesz zarządzać urządzeniami oznaczonymi jako **Microsoft Intune**. Jeśli urządzenie nie jest zarejestrowane w usłudze Microsoft Intune, opcja "Zarządzaj" zostanie wyszarzona.

![Zarządzanie urządzeniem usługi Intune](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Włączanie / wyłączanie urządzenia usługi Azure AD

Aby włączyć / wyłączyć urządzenie, masz dwie opcje:

- Menu zadania ("...") na stronie **Wszystkie urządzenia**

   ![Zarządzanie urządzeniem usługi Intune](./media/device-management-azure-portal/71.png)

- Pasek narzędzi na stronie **Urządzenia**

   ![Zarządzanie urządzeniem usługi Intune](./media/device-management-azure-portal/32.png)

**Uwagi:**

- Aby włączyć / wyłączyć urządzenie, musisz być administratorem globalnym lub administratorem urządzenia w chmurze w usłudze Azure AD. 
- Wyłączenie urządzenia uniemożliwia urządzeniu pomyślne uwierzytelnianie za pomocą usługi Azure AD, uniemożliwiając w ten sposób urządzeniu dostęp do zasobów usługi Azure AD, które są chronione przez urząd certyfikacji urządzenia lub przy użyciu poświadczeń WH4B.
- Wyłączenie urządzenia spowoduje odwołanie zarówno podstawowego tokenu odświeżania (PRT), jak i tokenów odświeżania (RT) na urządzeniu.

### <a name="delete-an-azure-ad-device"></a>Usuwanie urządzenia usługi Azure AD

Aby usunąć urządzenie, dostępne są dwie opcje:

- Menu zadania ("...") na stronie **Wszystkie urządzenia**

   ![Zarządzanie urządzeniem usługi Intune](./media/device-management-azure-portal/72.png)

- Pasek narzędzi na stronie **Urządzenia**

   ![Usuwanie urządzenia](./media/device-management-azure-portal/34.png)

**Uwagi:**

- Aby usunąć urządzenie, musisz być administratorem globalnym lub administratorem usługi Intune w usłudze Azure AD.
- Usuwanie urządzenia:
   - Uniemożliwia urządzeniu dostęp do zasobów usługi Azure AD.
   - Usuwa wszystkie szczegóły dołączone do urządzenia, na przykład klawisze Funkcji BitLocker dla urządzeń z systemem Windows.  
   - Reprezentuje działanie nie można odzyskać i nie jest zalecane, chyba że jest to wymagane.

Jeśli urządzenie jest zarządzane przez inny urząd zarządzania (na przykład Microsoft Intune), upewnij się, że urządzenie zostało wyczyszczone / wycofane przed usunięciem urządzenia w usłudze Azure AD. Przejrzyj, jak [zarządzać przestarzałymi urządzeniami](device-management-azure-portal.md) przed usunięciem jakichkolwiek urządzeń.

### <a name="view-or-copy-device-id"></a>Wyświetlanie lub kopiowanie identyfikatora urządzenia

Identyfikator urządzenia umożliwia zweryfikowanie szczegółów identyfikatora urządzenia na urządzeniu lub korzystanie z programu PowerShell podczas rozwiązywania problemów. Aby uzyskać dostęp do opcji kopiowania, kliknij urządzenie.

![Wyświetlanie identyfikatora urządzenia](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Wyświetlanie lub kopiowanie klawiszy funkcji BitLocker

Można wyświetlać i kopiować klucze funkcji BitLocker, aby ułatwić użytkownikom odzyskanie zaszyfrowanego dysku. Te klucze są dostępne tylko dla urządzeń z systemem Windows, które są szyfrowane i mają swoje klucze przechowywane w usłudze Azure AD. Klucze te można skopiować podczas uzyskiwania dostępu do szczegółów urządzenia.

![Wyświetlanie klawiszy funkcji BitLocker](./media/device-management-azure-portal/36.png)

Aby wyświetlić lub skopiować klucze funkcji BitLocker, musisz być właścicielem urządzenia lub użytkownikiem, którego przypisano co najmniej jedną z następujących ról:

- Administrator urządzeń w chmurze
- Administrator globalny
- Administrator działu pomocy technicznej
- Administrator usługi Intune
- Administrator zabezpieczeń
- Czytelnik zabezpieczeń

> [!NOTE]
> Hybrydowe urządzenia z systemem Windows 10 przyłączone do usługi Azure AD nie mają właściciela. Tak więc, jeśli szukasz urządzenia przez właściciela i nie znalazłeś go, wyszukaj według identyfikatora urządzenia.

## <a name="audit-logs"></a>Dzienniki inspekcji

Działania urządzenia są dostępne za pośrednictwem dzienników aktywności. Dzienniki te obejmują działania wyzwalane przez usługę rejestracji urządzeń i przez użytkowników:

- Tworzenie i dodawanie urządzeń właścicieli / użytkowników na urządzeniu
- Zmiany w ustawieniach urządzenia
- Operacje na urządzeniu, takie jak usuwanie lub aktualizowanie urządzenia

Punktem wejścia do danych inspekcji jest **Dzienniki inspekcji** w sekcji **Działania** na stronie **Urządzenia.**

Dziennik inspekcji ma domyślny widok listy, który pokazuje:

- Data i godzina wystąpienia
- Cele
- Inicjator / aktor (który) działania
- Aktywność (co)

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

Oprócz filtrów można wyszukiwać określone wpisy.

![Dzienniki inspekcji](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Następne kroki

[Jak zarządzać przestarzałymi urządzeniami w usłudze Azure AD](manage-stale-devices.md)
