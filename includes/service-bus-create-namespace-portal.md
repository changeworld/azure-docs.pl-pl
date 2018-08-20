---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 07/03/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 6fb9b5cd1379f39796f984f0436900f629c66436
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "40209864"
---
Aby rozpocząć korzystanie z obsługi wiadomości usługi Service Bus na platformie Azure, musisz najpierw utworzyć przestrzeń nazw o nazwie, która jest unikatowa w obrębie platformy Azure. Przestrzeń nazw zapewnia kontener określania zakresu na potrzeby adresowania zasobów usługi Service Bus w aplikacji.

Aby utworzyć przestrzeń nazw:

1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. W lewym okienku nawigacyjnym portalu kliknij pozycję **+ Utwórz zasób**, a następnie kliknij pozycje **Integracja** i **Service Bus**.
3. W oknie dialogowym **Tworzenie przestrzeni nazw** wprowadź nazwę przestrzeni nazw. System od razu sprawdza, czy nazwa jest dostępna.
4. Po upewnieniu się, że nazwa przestrzeni nazw jest dostępna, wybierz warstwę cenową (Podstawowa, Standardowa lub Premium). Jeśli chcesz korzystać z [tematów i subskrypcji](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), pamiętaj, aby wybrać warstwę Standardowa lub Premium. Tematy/subskrypcje nie są obsługiwane w warstwie cenowej Podstawowa.
5. W polu **Subskrypcja** wybierz subskrypcję platformy Azure, w której ma zostać utworzona przestrzeń nazw.
6. W polu **Grupa zasobów** wybierz istniejącą grupę zasobów, w której znajdzie się przestrzeń nazw, lub utwórz nową.      
7. W polu **Lokalizacja** wybierz kraj lub region, w którym powinna być hostowana przestrzeń nazw.
   
    ![Create namespace][create-namespace]
8. Kliknij pozycję **Utwórz**. W systemie zostanie utworzona i włączona przestrzeń nazw. Proces aprowizacji zasobów dla konta w systemie może potrwać kilka minut.

### <a name="obtain-the-management-credentials"></a>Uzyskiwanie poświadczeń zarządzania

Utworzenie nowej przestrzeni nazw powoduje automatyczne wygenerowanie początkowej reguły sygnatury dostępu współdzielonego ze skojarzoną parą kluczy podstawowego i pomocniczego, która przyznaje pełną kontrolę nad wszystkimi aspektami przestrzeni nazw. Zapoznaj się z artykułem [Uwierzytelnianie i autoryzacja w usłudze Service Bus](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md), aby dowiedzieć się, jak utworzyć dodatkowe reguły z bardziej ograniczonymi prawami dla zwykłych nadawców i odbiorców. Aby skopiować początkową regułę, wykonaj następujące kroki: 

1. Kliknij pozycję **Wszystkie zasoby**, a następnie kliknij nowo utworzoną nazwę przestrzeni nazw.
2. W oknie przestrzeni nazw kliknij pozycję **Zasady dostępu współdzielonego**.
3. Na ekranie **Zasady dostępu współdzielonego** kliknij pozycję **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. W oknie **Zasady: RootManageSharedAccessKey** kliknij przycisk kopiowania obok pozycji **Podstawowe parametry połączenia**, aby skopiować parametry połączenia do schowka w celu późniejszego użycia. Wklej tę wartość do Notatnika lub innej tymczasowej lokalizacji.
   
    ![connection-string][connection-string]

5. Powtórz poprzedni krok, kopiując i wklejając wartość pozycji **Klucz podstawowy** w lokalizacji tymczasowej do późniejszego użycia.

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
