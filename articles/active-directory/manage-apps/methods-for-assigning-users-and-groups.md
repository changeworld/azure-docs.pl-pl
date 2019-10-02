---
title: Jak przypisać użytkowników i grupy do aplikacji | Microsoft Docs
description: Przypisywanie użytkowników do aplikacji w celu udzielenia dostępu
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd66df381e11582b30ded5e3b529070357f35ad2
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720285"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Przypisywanie użytkowników i grup do aplikacji w Azure Active Directory
W tym artykule opisano sposób przypisywania użytkowników lub grup do aplikacji w Azure Active Directory (Azure AD). Użytkownicy muszą najpierw zostać przypisani do aplikacji, aby administrator mógł udzielić im dostępu do następujących czynności:

-   Uzyskaj dostęp do aplikacji, **przechodząc do adresu URL aplikacji bezpośrednio** (nazywanego także logowaniem z programu SP).

-   Dostęp do aplikacji przy użyciu **adresu URL dostępu użytkownika** na stronie **Właściwości** aplikacji (nazywanej także logowaniem inicjowanym przez dostawcy tożsamości).

-   Zobaczysz aplikację na [panelu dostępu do aplikacji](https://myapps.microsoft.com/) lub aplikacji mobilnej.

-   Zobacz, jak aplikacja jest wyświetlana na [ekranie uruchamiania aplikacji pakietu Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

Dostępność przypisań opartych na grupach jest określana na podstawie umowy licencyjnej. Przypisanie oparte na grupach jest obsługiwane tylko dla grup zabezpieczeń. Członkostwa w grupach zagnieżdżonych i grupy usługi O365 nie są obecnie obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne
Przed przypisaniem użytkowników i grup do aplikacji należy wymagać przypisania użytkownika. Aby wymagać przypisania użytkownika:

1. Zaloguj się do Azure Portal przy użyciu konta administratora.
2. Kliknij element **wszystkie usługi** w menu głównym.
3. Wybierz katalog, który jest używany przez aplikację.
4. Kliknij kartę **aplikacje dla przedsiębiorstw** .
5. Wybierz aplikację z listy aplikacji skojarzonych z tym katalogiem.
6. Kliknij kartę **Właściwości** .
7. Zmienić **wymagane przypisanie użytkownika?** Przełącz na tak.
8. Kliknij przycisk **Zapisz** w górnej części ekranu.

## <a name="assign-users"></a>Przypisywanie użytkowników

Aby bezpośrednio przypisać co najmniej jednego użytkownika do aplikacji, wykonaj następujące czynności:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny lub jako właściciel aplikacji niebędącej administratorami.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

    * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6.  Wybierz aplikację, do której chcesz przypisać użytkownika z listy.

7.  Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.

8.  Kliknij przycisk **Dodaj** na górze listy **Użytkownicy i grupy** , aby otworzyć okienko **Dodaj przypisanie** .

9.  Kliknij selektor **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

10. Wpisz **pełną nazwę** lub **adres e-mail** użytkownika, którego chcesz przypisać, w polu Wyszukaj **według nazwy lub adresu e-mail** .

11. Umieść kursor nad **użytkownikiem** na liście, aby odsłonić **pole wyboru**. Kliknij pole wyboru obok zdjęcia lub logo profilu użytkownika, aby dodać użytkownika do **wybranej** listy.

12. **Opcjonalne:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, wpisz inną **pełną nazwę** lub **adres e-mail** w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie kliknij pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Po zakończeniu wybierania użytkowników kliknij przycisk **Wybierz** , aby dodać do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **roli wybierz** w okienku **Dodaj przypisanie** , aby wybrać rolę, która ma zostać przypisana do wybranych użytkowników.

15. Kliknij przycisk **Przypisz** , aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie wybrane przez Ciebie użytkownicy będą mogli uruchamiać te aplikacje za pomocą metod opisanych w sekcji opis rozwiązania.

## <a name="assign-groups"></a>Przypisywanie grup

Aby bezpośrednio przypisać jedną lub więcej grup do aplikacji, wykonaj następujące czynności:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub jako właściciel aplikacji innej niż administracyjna z przypisaną licencją Azure AD — wersja Premium.

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

    * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6.  Wybierz aplikację, do której chcesz przypisać użytkownika z listy.

7.  Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.

8.  Kliknij przycisk **Dodaj** na górze listy **Użytkownicy i grupy** , aby otworzyć okienko **Dodaj przypisanie** .

9.  Kliknij selektor **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

10. Wpisz **pełną nazwę** grupy, którą chcesz przypisać, w polu Wyszukaj **według nazwy lub adresu e-mail** .

11. Umieść kursor nad **grupą** na liście, aby odsłonić **pole wyboru**. Kliknij pole wyboru obok zdjęcia lub logo profilu grupy, aby dodać użytkownika do **wybranej** listy.

12. **Opcjonalne:** Jeśli chcesz **dodać więcej niż jedną grupę**, wpisz inną **pełną nazwę grupy** w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie kliknij pole wyboru, aby dodać tę grupę do **wybranej** listy.

13. Po zakończeniu wybierania grup kliknij przycisk **Wybierz** , aby dodać do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **roli wybierz** w okienku **Dodaj przypisanie** , aby wybrać rolę, która ma zostać przypisana do wybranych grup.

15. Kliknij przycisk **Przypisz** , aby przypisać aplikację do wybranych grup.

Po krótkim czasie użytkownicy należący do wybranych grup będą mogli uruchamiać te aplikacje za pomocą metod opisanych w sekcji opis rozwiązania. Jeśli są to grupy dynamiczne, w przypadku użytkowników w tych przypisanych grupach mogą występować dodatkowe opóźnienia przetwarzania.

## <a name="enable-self-service-application-access"></a>Włącz dostęp do aplikacji samoobsługowej

Samoobsługowy dostęp do aplikacji to doskonały sposób na umożliwienie użytkownikom samodzielnego odnajdywania aplikacji, opcjonalnie Zezwalanie grupie biznesowej na zatwierdzanie dostępu do tych aplikacji. Można zezwolić grupie biznesowej na zarządzanie poświadczeniami przypisanymi do tych użytkowników w celu logowania jednokrotnego w aplikacjach bezpośrednio z poziomu panelu dostępu.

Aby włączyć samoobsługowy dostęp do aplikacji, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, z której chcesz włączyć dostęp do funkcji samoobsługi z listy.

7. Po załadowaniu aplikacji kliknij pozycję **samoobsługa** w menu nawigacji po lewej stronie aplikacji.

8. Aby włączyć samoobsługowy dostęp do aplikacji dla tej aplikacji, Włącz opcję **zezwól użytkownikom na żądanie dostępu do tej aplikacji?** Przełącz na **wartość tak.**

9. Aby wybrać grupę, do której mają zostać dodane użytkownicy żądający dostępu do tej aplikacji, kliknij selektor obok etykiety, **do której Grupa powinna zostać dodana** , a następnie wybierz grupę.

10. **Opcjonalne:** Jeśli chcesz wymagać zatwierdzenia biznesowego, zanim użytkownicy będą mieć dostęp, ustaw opcję **Wymagaj zatwierdzenia przed udzieleniem dostępu do tej aplikacji?** Przełącz na **wartość tak**.

11. **Opcjonalne: w przypadku aplikacji korzystających tylko z logowania** jednokrotnego, jeśli chcesz zezwolić tym osobom, aby osoby zatwierdzające firmy określiły hasła wysyłane do tej aplikacji dla zatwierdzonych użytkowników, ustaw dla tych osób **osoby zatwierdzające, aby ustawić dla nich hasła użytkownika Aplikacja?** Przełącz na **wartość tak**.

12. **Opcjonalne:** Aby określić osoby zatwierdzające w firmie, które mogą zatwierdzać dostęp do tej aplikacji, kliknij selektor obok etykiety, **która może zatwierdzić dostęp do tej aplikacji?** , aby wybrać maksymalnie 10 osób zatwierdzających w biznesie.

    >[!NOTE]
    >Grupy nie są obsługiwane.
    >
    >

13. **Opcjonalne:** **w przypadku aplikacji, które uwidaczniają role**, aby przypisać użytkowników, którzy mają zostać przypisani do roli, kliknij selektor obok elementu, **do którego rola powinna być przypisana do tej aplikacji?** aby wybrać rolę, do której Ci użytkownicy należy przypisać.

14. Kliknij przycisk **Zapisz** w górnej części okienka, aby zakończyć.

Po zakończeniu konfigurowania aplikacji samoobsługi użytkownicy mogą przechodzić do [panelu dostępu do aplikacji](https://myapps.microsoft.com/) i klikać przycisk **+ Dodaj** , aby znaleźć aplikacje, dla których włączono funkcję samoobsługowego dostępu. Osoby zatwierdzające firmy widzą również powiadomienie w [panelu dostępu do aplikacji](https://myapps.microsoft.com/). Możesz włączyć wiadomość e-mail z powiadomieniem, gdy użytkownik zażądał dostępu do aplikacji, która wymaga zatwierdzenia. 

Te zatwierdzenia obsługują tylko jednorazowe przepływy pracy zatwierdzania, co oznacza, że jeśli określisz wiele osób zatwierdzających, każda osoba zatwierdzająca może zatwierdzić dostęp do aplikacji.

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
