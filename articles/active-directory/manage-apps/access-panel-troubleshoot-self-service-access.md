---
title: Problem za pomocą Samoobsługowego dostępu do aplikacji | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów związanych z samoobsługowego dostępu do aplikacji
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a981dfb1d72c21eccf2ad7119ea219114ed15aed
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784281"
---
# <a name="problem-using-self-service-application-access"></a>Problem za pomocą Samoobsługowego dostępu do aplikacji

Opcjonalnie samoobsługowego dostępu do aplikacji jest to doskonały sposób, aby zezwolić użytkownikom na własnym odnajdywanie aplikacji, umożliwiają grupie biznesowej zatwierdzać dostęp do tych aplikacji. Możesz zezwolić grupie biznesowej do zarządzania poświadczeniami przypisane do tych użytkowników po prawej stronie hasło logowania jednokrotnego w aplikacji w swoich panelach dostępu.

Zanim użytkownicy mogą odnajdować samodzielnie aplikacje z panelu dostępu, musisz włączyć **samoobsługowego dostępu do aplikacji** do dowolnych aplikacji, które użytkownicy mogą samodzielnie odnajdywanie i żądania dostępu do.

## <a name="general-issues-to-check-first"></a>Zagadnienia ogólne w celu sprawdzenia

-   Upewnij się, że prawidłowo skonfigurowano samoobsługowego dostępu do aplikacji. Zobacz sekcję "Jak skonfigurować samoobsługowego dostępu do aplikacji".

-   Upewnij się, że użytkownik lub grupa został włączony, aby zażądać samoobsługowego dostępu do aplikacji.

-   Upewnij się, że użytkownik odwiedził prawidłowe miejsce samoobsługowego dostępu do aplikacji. Użytkownicy mogą przejść do ich [panelu dostępu do aplikacji](https://myapps.microsoft.com/) i kliknij przycisk **+ Dodaj** przycisk, aby znaleźć aplikacje, dla których włączono dostęp samoobsługowy.

-   Jeśli niedawno została skonfigurowana samoobsługowego dostępu do aplikacji, spróbuj zarejestrować i pomniejszać ponownie do panelu dostępu użytkownika po kilku minutach aby zobaczyć, jeśli zmiany dostęp samoobsługowy pojawiły się.

## <a name="how-to-configure-self-service-application-access"></a>Konfigurowanie samoobsługowego dostępu do aplikacji

Aby włączyć samoobsługowego dostępu do aplikacji do aplikacji, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure Portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

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
    > Grupy nie są obsługiwane.
    >
    >

13. **Opcjonalnie:** **W przypadku aplikacji, które ujawniają role**, jeśli chcesz przypisać samoobsługi dla zatwierdzonych użytkowników do roli, kliknij selektor **do jakiej roli powinni być przypisani użytkownicy w tej aplikacji?** do wybierz rolę, do którego należy je przypisywać tych użytkowników.

14. Kliknij przycisk **Zapisz** znajdujący się u góry bloku, aby zakończyć.

Po ukończeniu konfiguracji samoobsługowego aplikacji użytkownicy mogą przejść do ich [panelu dostępu do aplikacji](https://myapps.microsoft.com/) i kliknij przycisk **+ Dodaj** przycisk, aby znaleźć aplikacje, dla których włączono Samoobsługowe dostęp. Osoby zatwierdzające w firmie również wyświetlone powiadomienie w ich [panelu dostępu do aplikacji](https://myapps.microsoft.com/). Można włączyć wiadomość e-mail z powiadomieniem je, gdy użytkownik poprosi o dostęp do aplikacji, która wymaga zatwierdzenia. 

Te zatwierdzenia obsługuje pojedynczy przepływy pracy zatwierdzania, co oznacza, że jeśli określisz wiele osób zatwierdzających, wszelkie jedną osobą zatwierdzającą może zatwierdzić dostęp do aplikacji.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiąże problemu 

Otwórz bilet pomocy technicznej, używając następujących informacji, jeśli jest dostępny:

-   Identyfikator błędu korelacji

-   Nazwy UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Występuje, strefę czasową i przedziału czasu/czasu podczas błędu

-   Ślady narzędzia fiddler

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie usługi Azure Active Directory do samoobsługowego zarządzania grupami](../users-groups-roles/groups-self-service-management.md)
