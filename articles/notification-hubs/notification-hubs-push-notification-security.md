---
title: Model zabezpieczeń Centra powiadomień
description: Dowiedz się więcej o modelu zabezpieczeń dla usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: b871775bc7a6d795e86147ae9cffa27bdd2f3348
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263765"
---
# <a name="notification-hubs-security"></a>Zabezpieczenia centrów powiadomień

## <a name="overview"></a>Omówienie

W tym temacie opisano model zabezpieczeń usługi Azure Notification Hubs.

## <a name="shared-access-signature-security"></a>Zabezpieczenia podpisu dostępu współdzielonego

Centra powiadomień implementują schemat zabezpieczeń na poziomie jednostki o nazwie *Sygnatura dostępu współdzielonego (SAS).* Każda reguła zawiera nazwę, wartość klucza (wspólny klucz tajny) i zestaw praw, jak wyjaśniono później w [oświadczenia zabezpieczeń](#security-claims). 

Podczas tworzenia koncentratora automatycznie tworzone są dwie reguły: jedna z prawami **nasłuchiwania** (używana przez aplikację kliencką) i jedna z **wszystkimi** prawami (używana przez zaplecze aplikacji):

- **DefaultListenSharedAccessSignature**: udziela tylko uprawnień **Do słuchania.**
- **DefaultFullSharedAccessSignature**: udziela uprawnień **Do słuchania,** **zarządzania**i **wysyłania.** Ta zasada ma być używana tylko w wewnętrznej cemie aplikacji. Nie należy go używać w aplikacjach klienckich; użyj zasad z dostępem tylko **do nasłuchiwać.** Aby utworzyć nową zasadę dostępu niestandardowego z nowym tokenem sygnatury dostępu Współdzielonego, zobacz [tokeny sygnatury dostępu współdzielonego dla zasad dostępu](#sas-tokens-for-access-policies) w dalszej części tego artykułu.

Podczas wykonywania zarządzania rejestracją z aplikacji klienckich, jeśli informacje wysyłane za pośrednictwem powiadomień nie są poufne (na przykład aktualizacje pogody), typowym sposobem dostępu do Centrum powiadomień jest nadanie kluczowej wartości reguły Dostęp tylko do nasłuchiwać do aplikacji klienckiej, i aby nadać kluczową wartość reguły pełny dostęp do wewnętrznej bazy danych aplikacji.

Aplikacje nie powinny osadzać wartości klucza w aplikacjach klienckich ze Sklepu Windows; zamiast tego aplikacja kliencka pobrać go z wewnętrznej bazy danych aplikacji podczas uruchamiania.

Klucz z dostępem do **nasłuchiwać** umożliwia aplikacji klienckiej rejestrowanie dowolnego tagu. Jeśli aplikacja musi ograniczyć rejestracje do określonych tagów do określonych klientów (na przykład, gdy tagi reprezentują identyfikatory użytkowników), wewnętrznej bazy danych aplikacji należy wykonać rejestracje. Aby uzyskać więcej informacji, zobacz [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md). Należy zauważyć, że w ten sposób aplikacja kliencka nie będzie miała bezpośredniego dostępu do centrów powiadomień.

## <a name="security-claims"></a>Oświadczenia zabezpieczające

Podobnie jak w przypadku innych jednostek, operacje Centrum powiadomień są dozwolone dla trzech oświadczeń zabezpieczeń: **Nasłuchij,** **Wyślij**i **Zarządzaj**.

| Claim   | Opis                                          | Dozwolone operacje |
| ------- | ---------------------------------------------------- | ------------------ |
| Nasłuchiwanie  | Tworzenie/aktualizowanie, odczytywanie i usuwanie pojedynczych rejestracji | Tworzenie/aktualizowanie rejestracji<br><br>Przeczytaj rejestrację<br><br>Przeczytaj wszystkie rejestracje dla dojścia<br><br>Usuń rejestrację |
| Wysyłanie    | Wysyłanie wiadomości do Centrum powiadomień                | Wyślij wiadomość |
| Zarządzaj  | CruDs w Centrach powiadomień (w tym aktualizowanie poświadczeń usługi PNS i kluczy zabezpieczeń) oraz odczytywanie rejestracji na podstawie tagów |Centra tworzenia/aktualizacji/odczytu/usuwania<br><br>Odczytywanie rejestracji według tagów |

Centra powiadomień akceptuje tokeny sygnatury dostępu Współdzielonego wygenerowane za pomocą kluczy udostępnionych skonfigurowanych bezpośrednio w centrum.

Nie jest możliwe wysłanie powiadomienia do więcej niż jednego obszaru nazw. Przestrzenie nazw są kontenerami logicznymi dla centrów powiadomień i nie są zaangażowane w wysyłanie powiadomień.

Użyj zasad dostępu na poziomie obszaru nazw (poświadczeń) dla operacji na poziomie obszaru nazw; na przykład: wyświetlanie hubów, tworzenie lub usuwanie koncentratorów itp. Tylko zasady dostępu na poziomie centrum umożliwiają wysyłanie powiadomień.

### <a name="sas-tokens-for-access-policies"></a>Tokeny sygnatury dostępu współdzielonego dla zasad dostępu

Aby utworzyć nowe oświadczenie zabezpieczeń lub wyświetlić istniejące klucze Sygnatury dostępu Współdzielonego, wykonaj następujące czynności:

1. Zaloguj się do Portalu Azure.
2. Wybierz pozycję **Wszystkie zasoby**.
3. Wybierz nazwę Centrum powiadomień, dla którego chcesz utworzyć oświadczenie lub wyświetlić klucz Sygnatury dostępu Współdzielonego.
4. W menu po lewej stronie wybierz pozycję **Zasady dostępu**.
5. Wybierz **pozycję Nowa zasada,** aby utworzyć nowe oświadczenie zabezpieczeń. Nadaj zasadom nazwę i wybierz uprawnienia, które chcesz przyznać. Następnie wybierz przycisk **OK**.
6. Pełny ciąg połączenia (łącznie z nowym kluczem Sygnatury dostępu współdzielonego) jest wyświetlany w oknie Zasady dostępu. Ten ciąg można skopiować do schowka w celu późniejszego użycia.

Aby wyodrębnić klucz Sygnatury dostępu Współdzielonego z określonych zasad, wybierz przycisk **Kopiuj** obok zasady zawierającej odpowiedni klucz sygnatury dostępu Współdzielonego. Wklej tę wartość do lokalizacji tymczasowej, a następnie skopiuj część klucza sygnatury dostępu Współdzielonego ciągu połączenia. W tym przykładzie użyto obszaru nazw Centrów powiadomień o nazwie **mytestnamespace1**i zasad o nazwie **policy2**. Klucz Sygnatury dostępu Współdzielonego jest wartością na końcu ciągu określoną przez **SharedAccessKey:**

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![Pobierz klucze SAS](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Następne kroki

- [Omówienie Centrów powiadomień](notification-hubs-push-notification-overview.md)
