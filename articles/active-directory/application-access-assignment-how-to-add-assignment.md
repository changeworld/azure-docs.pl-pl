---
title: Jak przypisać użytkowników i grup do aplikacji | Dokumentacja firmy Microsoft
description: Przypisywanie użytkowników do aplikacji w celu udzielenia dostępu
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 19303237e7e2c3c3a7f373102465875c34a571d1
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068426"
---
# <a name="how-to-assign-users-and-groups-to-an-application"></a>Jak przypisać użytkowników i grup do aplikacji

Zanim użytkownicy można wykonać następujące czynności dla określonej aplikacji, należy najpierw **przypisać je do aplikacji** Aby udzielić im dostępu:

-   Uzyskiwanie dostępu do aplikacji przez **nawigowanie do adresu URL aplikacji bezpośrednio** (nazywany także inicjowane SP logowania jednokrotnego).

-   Dostęp do aplikacji przy użyciu **adres URL dostępu użytkownika** w aplikacji **właściwości** strony (nazywany także inicjowane IDP logowania).

-   Zobacz aplikacji są wyświetlane na ich [panelu dostępu aplikacji](https://myapps.microsoft.com/) lub aplikacji mobilnej.

-   Zobacz aplikacji są wyświetlane na ich [uruchamiający aplikację usługi Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

## <a name="methods-to-assign-applications-with-azure-active-directory"></a>Metody przypisania aplikacji za pomocą usługi Azure Active Directory 

Istnieją 3 sposoby można przypisać aplikacji za pomocą usługi Azure Active Directory:

-   [Przypisywanie użytkownika bezpośrednio do aplikacji jako administrator](#assign-a-user-directly-as-an-administrator)

-   [Przypisz grupę bezpośrednio do aplikacji jako administrator](#assign-a-group-directly-to-an-application-as-an-administrator)

-   [Włącz dostęp do aplikacji Sklep internetowy umożliwia użytkownikom znajdowanie własne aplikacje](#enable-self-service-application-access-to-allow-users-to-find-their-own-applications)

## <a name="assign-a-user-directly-as-an-administrator"></a>Przypisywanie użytkownika bezpośrednio jako administrator

Aby przypisać bezpośrednio co najmniej jednego użytkownika do aplikacji, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji głównego po lewej stronie.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikacji, którą chcesz przypisać do użytkownika z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** z menu nawigacji po lewej stronie aplikacji.

8.  Kliknij przycisk **Dodaj** przycisk nad **użytkowników i grup** listy, aby otworzyć **Dodaj przydziału** okienka.

9.  Kliknij przycisk **użytkowników i grup** selektora z **Dodaj przydziału** okienka.

10. Wpisz w **Pełna nazwa** lub **adres e-mail** użytkownika planuje się przypisanie do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Zaznacz pole wyboru obok zdjęcia profilu użytkownika lub logo, aby dodać użytkownika do **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, typu w innym **Pełna nazwa** lub **adres e-mail** do **wyszukiwania według nazwy lub adres e-mail** polu wyszukiwania, a następnie kliknij przycisk wyboru, aby dodać użytkownika do **wybrane** listy.

13. Po zakończeniu wybierania użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup, które ma być przypisany do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektora w **Dodaj przydziału** okienku wybierz rolę do przypisania do wybranych użytkowników.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie użytkowników, dla których wybrano mieć możliwość uruchamiania tych aplikacji za pomocą metod opisanych w sekcji Opis rozwiązania.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Przypisz grupę bezpośrednio do aplikacji jako administrator

Aby przypisać co najmniej jedną grupę aplikacji bezpośrednio, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji głównego po lewej stronie.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikacji, którą chcesz przypisać do użytkownika z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** z menu nawigacji po lewej stronie aplikacji.

8.  Kliknij przycisk **Dodaj** przycisk nad **użytkowników i grup** listy, aby otworzyć **Dodaj przydziału** okienka.

9.  Kliknij przycisk **użytkowników i grup** selektora z **Dodaj przydziału** okienka.

10. Wpisz w **grupy Pełna nazwa** planuje się przypisanie do grupy **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **grupy** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok profilu zdjęcie lub logo, aby dodać użytkownika do grupy **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jedną grupę**, typu w innym **grupy Pełna nazwa** do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania i Kliknij pole wyboru, aby dodać tę grupę do **wybrane** listy.

13. Po wybraniu grup kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup, które ma być przypisany do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektora w **Dodaj przydziału** okienko, aby wybrać rolę można przypisać do wybranych grup.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych grup.

Po krótkim czasie użytkowników w grupach, który wybrano mieć możliwość uruchamiania tych aplikacji za pomocą metod opisanych w sekcji Opis rozwiązania. Jeśli są one grup dynamicznych, mogą występować pewne opóźnienie dodatkowego przetwarzania w tych przydziałów dla użytkowników w tych przypisanych grup.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Włącz dostęp do aplikacji Sklep internetowy umożliwia użytkownikom znajdowanie własne aplikacje

Dostęp do aplikacji Sklep internetowy jest to dobry sposób, aby zezwolić użytkownikom na własnym odnajdywanie aplikacji, opcjonalnie zezwolić grupie biznesowej, aby zatwierdzić dostęp do tych aplikacji. Możesz zezwolić grupie biznesowej do zarządzania poświadczeniami przypisane do tych użytkowników do prawej jednokrotnego hasła w aplikacji z ich paneli dostępu.

Aby włączyć samoobsługowe aplikacji dostęp do aplikacji, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji głównego po lewej stronie.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby umożliwić samoobsługi dostęp do z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **samoobsługi** z menu nawigacji po lewej stronie aplikacji.

8.  Aby włączyć dostęp do aplikacji Sklep internetowy dla tej aplikacji, należy włączyć **Zezwalaj użytkownikom na żądanie dostępu do tej aplikacji?** Przełącz, aby **tak.**

9.  Następnie wybierz grupę, do których użytkownicy, którzy żądają dostępu do tej aplikacji można dodać, kliknij przycisk wyboru obok etykiety **do grupy, do której ma zostać dodany przypisanych użytkowników?** i wybierz grupę.

10. **Opcjonalnie:** aby wymagają zatwierdzenia biznesowych, przed użytkownicy mają dostęp, ustaw **wymagają zatwierdzenia przed udzieleniem im dostępu do tej aplikacji?** Przełącz, aby **tak**.

11. **Opcjonalnie: dla aplikacji za pomocą hasła jednokrotnego na tylko** Jeśli chcesz umożliwić tych osób zatwierdzających firm określić hasła, które są wysyłane do tej aplikacji dla zatwierdzonych użytkowników, ustawić **Zezwalaj osób zatwierdzających do ustawienia użytkownika hasła dla tej aplikacji?**  Przełącz, aby **tak**.

12. **Opcjonalnie:** do określenia osób zatwierdzających biznesowych, którzy mogą zatwierdzić dostęp do tej aplikacji, kliknij przycisk wyboru obok etykiety **kto może zatwierdzić dostęp do tej aplikacji?** do wybranych do 10 osób zatwierdzających biznesowych.

  >[!NOTE]
  >Grupy nie są obsługiwane.
  >
  >

13. **Opcjonalnie:** **dla aplikacji, które ujawnia ról**, jeśli chcesz przypisać do roli użytkowników samoobsługi zatwierdzone, kliknij selektor **do roli należy użytkowników można przypisać w tej aplikacji?** Wybierz rolę, do której można przypisać tych użytkowników.

14. Kliknij przycisk **zapisać** w górnej części okienka, aby zakończyć.

Po zakończeniu konfiguracji samoobsługi aplikacji, użytkownicy mogą przechodzić do ich [panelu dostępu aplikacji](https://myapps.microsoft.com/) i kliknij przycisk **+ Dodaj** przycisk, aby znaleźć aplikacji, dla których włączono samoobsługi dostępu. Osób zatwierdzających firm również wyświetlone powiadomienie w ich [panelu dostępu aplikacji](https://myapps.microsoft.com/). Można włączyć wiadomość e-mail z informacją, gdy użytkownik żąda dostępu do aplikacji, która wymaga zatwierdzenia. 

Te zatwierdzenia obsługuje pojedynczy przepływów pracy, co oznacza, że jeśli określisz wiele osób zatwierdzających żadnych jedna osoba zatwierdzająca może osoba zatwierdzająca dostęp do aplikacji.

## <a name="next-steps"></a>Kolejne kroki
[Podaj logowanie jednokrotne do aplikacji przy użyciu serwera Proxy aplikacji](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
