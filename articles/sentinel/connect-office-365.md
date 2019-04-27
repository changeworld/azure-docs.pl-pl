---
title: Łączenie danych w usłudze Office 365 przez wartownika platformy Azure w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie z danych usługi Office 365 przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 77587b0b7506ef0ccadbeb6d1f010f5b6a72d93e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714561"
---
# <a name="connect-data-from-office-365-logs"></a>Połącz dane z pakietu Office 365 dzienników

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Można przesyłać strumieniowo dzienniki inspekcji z [usługi Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) do platformy Azure przez wartownika za pomocą jednego kliknięcia. Można przesyłać strumieniowo dzienniki inspekcji z wieloma dzierżawcami do jednego obszaru roboczego w przez wartownika platformy Azure. Łącznik dziennika aktywności usługi Office 365 zapewnia wgląd w działania użytkowników bieżące. Otrzymasz informacje o różnych, administrator, system i akcje dotyczące zasad i wydarzeń użytkownika z usługi Office 365. Łącząc dzienniki usługi Office 365 na platformie Azure przez wartownika można użyć tych danych do wyświetlania pulpitów nawigacyjnych, tworzyć niestandardowe alerty i poprawić procesem dochodzenia.


## <a name="prerequisites"></a>Wymagania wstępne

- Musi być administratorem globalnym lub administratorem zabezpieczeń, które znajdują się w dzierżawie
- Na komputerze, z którego zalogowano się do platformy Azure przez wartownika do utworzenia połączenia, upewnij się, że jest otwarty na potrzeby ruchu sieciowego port 4433.

## <a name="connect-to-office-365"></a>Łączenie z usługą Office 365

1. Na platformie Azure przez wartownika, wybierz **łączników danych** a następnie kliknij przycisk **usługi Office 365** kafelka.

2. Jeśli użytkownik nie jest jeszcze włączone, w obszarze **połączenia** użyj **Włącz** przycisk, aby włączyć rozwiązanie usługi Office 365. Jeśli został już włączony, zostaną zidentyfikowane na ekranie połączenia, jak jeszcze włączone.
1. Usługi Office 365 umożliwia przesyłanie strumieniowe danych z wielu dzierżaw do platformy Azure przez wartownika. Dla każdej dzierżawy, aby nawiązać połączenie, dodać dzierżawy w ramach **nawiązać połączenie z dzierżawy usługi Azure przez wartownika**. 
1. Zostanie otwarty ekran usługi Active Directory. Monit o uwierzytelnianie za pomocą użytkownik będący administratorem globalnym w każdej dzierżawy, którą chcesz nawiązać połączenie z platformy Azure przez wartownika i zapewniają uprawnienia do platformy Azure przez wartownika można odczytać jej dzienników. 
5. W obszarze dzienników aktywności usługi Office 365 Stream, kliknij **wybierz** można zdecydować, jakie typy dzienników, które mają być przesyłane strumieniowo do usługi Azure przez wartownika. Obecnie usługa Azure przez wartownika obsługuje programy Exchange i SharePoint.

4. Kliknij przycisk **Zastosuj zmiany**.

3. Aby użyć odpowiednich schematu w usłudze Log Analytics, w przypadku dzienników usługi Office 365, możesz wyszukać **OfficeActivity**.


## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób łączenia usługi Office 365 na platformie Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).

