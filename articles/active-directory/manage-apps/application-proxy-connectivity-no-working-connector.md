---
title: Nie znaleziono grupy łączników roboczych dla aplikacji proxy aplikacji
description: Rozwiązywanie problemów, które mogą wystąpić, gdy nie ma działającego łącznika w grupie łączników dla aplikacji za pomocą serwera proxy aplikacji usługi Azure AD
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
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96ab0d1b3bbab9c97c04da4f918f3aaa2f1d07e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275619"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nie znaleziono grupy łączników roboczych dla aplikacji proxy aplikacji

Ten artykuł pomaga rozwiązać typowe problemy napotykane, gdy nie wykryto łącznika dla aplikacji serwera proxy aplikacji zintegrowanej z usługą Azure Active Directory.

## <a name="overview-of-steps"></a>Omówienie kroków
Jeśli w grupie łączników dla aplikacji nie ma działającego łącznika, istnieje kilka sposobów rozwiązania problemu:

-   Jeśli w grupie nie ma łączników, możesz:

    -   Pobierz nowy łącznik po prawej stronie na serwerze lokalnym i przypisz go do tej grupy

    -   Przenoszenie aktywnego łącznika do grupy

-   Jeśli w grupie nie ma aktywnych łączników, możesz:

    -   Zidentyfikuj przyczynę, dla którego łącznik jest nieaktywny, i rozwiąż

    -   Przenoszenie aktywnego łącznika do grupy

Aby rozwiązać ten problem, otwórz menu "Serwer proxy aplikacji" w aplikacji i przyjrzyj się komunikatowi ostrzegawczemu grupy łączników. Jeśli w grupie nie ma żadnych łączników, komunikat ostrzegawczy określa, że grupa potrzebuje co najmniej jednego łącznika. Jeśli nie masz aktywnych łączników, komunikat ostrzegawczy wyjaśnia, że. Często mają nieaktywne łączniki. 

   ![Wybór grupy łączników w witrynie Azure portal](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Szczegółowe informacje na temat każdej z tych opcji można znaleźć w odpowiedniej sekcji poniżej. Instrukcje zakładają, że zaczynasz od strony zarządzanie łącznikami. Jeśli przeglądasz powyższy komunikat o błędzie, możesz przejść do tej strony, klikając komunikat ostrzegawczy. Możesz również przejść do strony, przechodząc do **usługi Azure Active Directory,** klikając pozycję **Aplikacje przedsiębiorstwa**, a następnie **serwer proxy aplikacji.**

   ![Zarządzanie grupami łączników w witrynie Azure portal](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Pobieranie nowego łącznika

Aby pobrać nowy łącznik, użyj przycisku "Pobierz łącznik" u góry strony.

Zainstaluj złącze na komputerze z bezpośrednim celownikiem do aplikacji wewnętrznej bazy danych. Zazwyczaj łącznik jest zainstalowany na tym samym serwerze co aplikacja. Po pobraniu łącznik powinien pojawić się w tym menu. kliknij łącznik i użyj listy rozwijanej "Grupa łączników", aby upewnić się, że należy ona do odpowiedniej grupy. Zapisz zmianę.

   ![Pobieranie łącznika z portalu Azure](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Przenoszenie aktywnego łącznika

Jeśli masz aktywny łącznik, który powinien należeć do grupy i ma linię wzroku do docelowej aplikacji wewnętrznej bazy danych, można przenieść łącznik do przypisanej grupy. Aby to zrobić, kliknij łącznik. W polu "Grupa łączników" użyj listy rozwijanej, aby wybrać właściwą grupę, a następnie kliknij przycisk Zapisz.

## <a name="resolve-an-inactive-connector"></a>Rozwiązywanie problemu z nieaktywnym łącznikiem

Jeśli tylko łączniki w grupie są nieaktywne, są one prawdopodobnie na komputerze, który nie ma wszystkie niezbędne porty odblokowane.

Zobacz porty Rozwiązywanie problemów z dokumentem, aby uzyskać szczegółowe informacje na temat badania tego problemu.

## <a name="next-steps"></a>Następne kroki
[Opis łączników serwera proxy aplikacji usługi Azure AD](application-proxy-connectors.md)


