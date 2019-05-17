---
title: Jak przypisać użytkowników i grup do aplikacji | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 7402778ec8170e60a623255d0d331f12d8389227
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824299"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Przypisywanie użytkowników i grup do aplikacji w usłudze Azure Active Directory
W tym artykule pokazano, jak przypisać użytkowników lub grup do aplikacji w usłudze Azure Active Directory (Azure AD). Użytkownicy najpierw muszą być przypisani do aplikacji, zanim administrator może nadać im dostęp do następujących:

-   Dostęp do aplikacji przez **przechodząc do adresu URL aplikacji bezpośrednio** (znany także jako zainicjowanego przez dostawcę usług logowania jednokrotnego).

-   Dostęp do aplikacji przy użyciu **adres URL dostępu użytkownika** aplikacji **właściwości** strony (nazywane również inicjowane przez dostawcę tożsamości logowanie jednokrotne).

-   Zobacz aplikacji pojawiają się na ich [panelu dostępu do aplikacji](https://myapps.microsoft.com/) lub aplikacji mobilnej.

-   Zobacz aplikacji pojawiają się na ich [uruchamianie aplikacji usługi Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

Dostępność przypisywania na podstawie grupy jest określana przez umowy licencyjnej. Przypisanie oparte na grupach jest obsługiwana tylko grupy zabezpieczeń i członkostwo w grupach zagnieżdżonych nie są obecnie obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne
Zanim będzie można przypisać użytkowników i grup do aplikacji, możesz wymagać od użytkownika przypisania. Wymaganie przypisania użytkownika:

1. Zaloguj się do witryny Azure portal przy użyciu konta administratora.
2. Kliknij pozycję **wszystkich usług** element w menu głównym.
3. Wybierz katalog, którego używasz dla aplikacji.
4. Kliknij pozycję **aplikacje dla przedsiębiorstw** kartę.
5. Wybierz aplikację z listy aplikacji skojarzonych z tym katalogiem.
6. Kliknij przycisk **właściwości** kartę.
7. Zmiana **wymagane przypisanie użytkownika?** Przełącz Yes (tak).
8. Kliknij przycisk **Zapisz** znajdujący się u góry ekranu.

## <a name="assign-users"></a>Przypisywanie użytkowników

Aby przypisać co najmniej jednego użytkownika do aplikacji bezpośrednio, wykonaj następujące czynności:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu nawigacji głównego po lewej stronie ekranu.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z poziomu menu nawigacji po lewej stronie ekranu w usłudze Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

    * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6.  Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** z poziomu menu nawigacji po lewej stronie ekranu w aplikacji.

8.  Kliknij przycisk **Dodaj** przycisk w górnej części **użytkowników i grup** liście, aby otworzyć **Dodaj przydziału** okienka.

9.  Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Pełna nazwa** lub **adres e-mail** użytkownika, jesteś zainteresowany przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok logo, aby dodać użytkownika, aby lub zdjęcie w profilu użytkownika **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, typ w innym **Pełna nazwa** lub **adres e-mail** do **wyszukiwanie według nazwy lub adresu e-mail** pole wyszukiwania, a następnie kliknij pole wyboru, aby dodać użytkownika do **wybrane** listy.

13. Gdy to zrobisz, Wybieranie użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę, aby przypisać użytkownikom wybrania.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie użytkowników, dla których wybrano mogli uruchamiać te aplikacje za pomocą metod opisanych w sekcji Opis rozwiązania.

## <a name="assign-groups"></a>Przypisywanie grup

Aby przypisać co najmniej jedną grupę aplikacji bezpośrednio, wykonaj następujące czynności:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu nawigacji głównego po lewej stronie ekranu.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z poziomu menu nawigacji po lewej stronie ekranu w usłudze Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

    * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6.  Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** z poziomu menu nawigacji po lewej stronie ekranu w aplikacji.

8.  Kliknij przycisk **Dodaj** przycisk w górnej części **użytkowników i grup** liście, aby otworzyć **Dodaj przydziału** okienka.

9.  Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Nazwa grupy pełną** grupy osób zainteresowanych przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **grupy** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok zdjęcie w profilu lub logo, aby dodać użytkownika do grupy **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednej grupy**, typ w innym **Nazwa grupy pełną** do **wyszukiwanie według nazwy lub adresu e-mail** polu wyszukiwania, a następnie kliknij pole wyboru, aby dodać tę grupę Aby **wybrane** listy.

13. Po wybraniu grup kliknij **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę do przypisania do wybranych grupach.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych grup.

Po krótkim czasie użytkowników w grupach, który wybrano mogli uruchamiać te aplikacje za pomocą metod opisanych w sekcji Opis rozwiązania. Jeśli te są grupami dynamicznymi, w tych przydziałów dla użytkowników w ramach tych przypisanych grup może istnieć pewne opóźnienie dodatkowego przetwarzania.

## <a name="enable-self-service-application-access"></a>Włącz samoobsługowego dostępu do aplikacji

Opcjonalnie samoobsługowego dostępu do aplikacji jest to doskonały sposób, aby zezwolić użytkownikom na własnym odnajdywanie aplikacji, umożliwiają grupie biznesowej zatwierdzać dostęp do tych aplikacji. Możesz zezwolić grupie biznesowej do zarządzania poświadczeniami przypisane do tych użytkowników po prawej stronie hasło logowania jednokrotnego w aplikacji w swoich panelach dostępu.

Aby włączyć samoobsługowego dostępu do aplikacji do aplikacji, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu nawigacji głównego po lewej stronie ekranu.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** z poziomu menu nawigacji po lewej stronie ekranu w usłudze Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, aby umożliwić Samoobsługowe dostęp do, z listy.

7. Po załadowaniu aplikacji, kliknij przycisk **samoobsługi** z poziomu menu nawigacji po lewej stronie ekranu w aplikacji.

8. Aby włączyć samoobsługowego dostępu do aplikacji dla tej aplikacji, należy wyłączyć **zezwalać użytkownikom na żądanie dostępu do tej aplikacji?** Przełącz, aby **tak.**

9. Następnie, aby wybrać grupę, do których użytkownicy, którzy żądają dostępu do tej aplikacji można dodać, kliknij selektor obok etykiety **grupę, do której należy dodać przypisanych użytkowników?** i wybrać grupę.

10. **Opcjonalnie:** Jeśli chcesz wymagane zatwierdzenie firmy, zanim użytkownicy mogą dostęp, należy ustawić **wymagają zatwierdzenia, zanim zostanie przyznany dostęp do tej aplikacji?** Przełącz, aby **tak**.

11. **Opcjonalnie: Dla aplikacji za pomocą logowania jednokrotnego hasła na tylko** Jeśli chcesz zezwolić na te osoby zatwierdzające w firmie określić hasła, które są wysyłane do tej aplikacji dla zatwierdzonych użytkowników, ustawić **zezwalać osobom zatwierdzającym Ustawianie haseł użytkownika dla tego Aplikacja?**  Przełącz, aby **tak**.

12. **Opcjonalnie:** Aby określić osoby zatwierdzające w firmie, którzy mogą zatwierdzać dostęp do tej aplikacji, kliknij selektor obok etykiety **kto może zatwierdzać dostęp do tej aplikacji?** wybrać maksymalnie 10 osoby zatwierdzające w firmie indywidualnych.

    >[!NOTE]
    >Grupy nie są obsługiwane.
    >
    >

13. **Opcjonalnie:** **W przypadku aplikacji, które ujawniają role**, jeśli chcesz przypisać samoobsługi dla zatwierdzonych użytkowników do roli, kliknij selektor **do jakiej roli powinni być przypisani użytkownicy w tej aplikacji?** do wybierz rolę, do którego należy je przypisywać tych użytkowników.

14. Kliknij przycisk **Zapisz** znajdujący się u góry okienka, aby zakończyć.

Po ukończeniu konfiguracji samoobsługowego aplikacji użytkownicy mogą przejść do ich [panelu dostępu do aplikacji](https://myapps.microsoft.com/) i kliknij przycisk **+ Dodaj** przycisk, aby znaleźć aplikacje, dla których włączono Samoobsługowe dostęp. Osoby zatwierdzające w firmie również wyświetlone powiadomienie w ich [panelu dostępu do aplikacji](https://myapps.microsoft.com/). Można włączyć wiadomość e-mail z powiadomieniem je, gdy użytkownik poprosi o dostęp do aplikacji, która wymaga zatwierdzenia. 

Te zatwierdzenia obsługuje pojedynczy przepływów pracy, co oznacza, że jeśli określisz wiele osób zatwierdzających, wszelkie jedną osobą zatwierdzającą może osoba zatwierdzająca dostęp do aplikacji.

## <a name="next-steps"></a>Kolejne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
