---
title: Notification Hubs zabezpieczeń
description: W tym temacie wyjaśniono zabezpieczeń usługi Azure notification hubs.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 22494984ca45cde7255fb5e1a30548c859bfad68
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826520"
---
# <a name="security-model-of-azure-notification-hubs"></a>Model zabezpieczeń usługi Azure Notification hubs

## <a name="overview"></a>Omówienie

W tym temacie opisano model zabezpieczeń usługi Azure Notification hubs. Ponieważ usługa Notification Hubs znajdują się jednostki usługi Service Bus, implementują ten sam model zabezpieczeń, co Usługa Service Bus. Aby uzyskać więcej informacji, zobacz [uwierzytelniania w usłudze Service Bus](https://msdn.microsoft.com/library/azure/dn155925.aspx) tematów.

## <a name="shared-access-signature-security-sas"></a>Zabezpieczenia sygnatury dostępu współdzielonego (SAS)

Notification Hubs implementuje schematu zabezpieczenia na poziomie jednostki o nazwie SAS (Shared Access Signature). Ten schemat pozwala jednostek obsługi komunikatów zadeklarować maksymalnie 12 reguły autoryzacji w swoim opisie, które udzielić praw tej jednostki.

Każda reguła zawiera nazwę, wartość klucza (wspólny klucz tajny) i zestaw praw, zgodnie z opisem w sekcji "Oświadczeń zabezpieczeń". Podczas tworzenia Centrum powiadomień, dwie reguły są tworzone automatycznie: jeden z prawa do nasłuchiwania, (które aplikacja klienta używa) i jeden z wszystkich uprawnień (korzysta z zapleczem aplikacji).

Podczas przeprowadzania Zarządzanie rejestracją w aplikacjach klienckich, jeśli informacje są wysyłane za pośrednictwem powiadomienia nie jest wielkość liter (na przykład pogoda aktualizacji), typowym sposobem dostęp do danego centrum powiadomień jest oferowanie wartość klucza reguły dostępu tylko do nasłuchiwania aplikacji klienckiej i podać wartość klucza reguły pełny dostęp do zaplecza aplikacji.

Osadzanie wartość klucza w aplikacji klienta Windows Store nie jest zalecane. Sposób, aby uniknąć osadzenia wartość klucza jest aplikacja kliencka, pobierz ją z zaplecza aplikacji przy uruchamianiu.

Jest ważne dowiedzieć się, że klucz z dostępem do nasłuchiwania zezwala na aplikację kliencką do zarejestrowania dla każdego znacznika. Jeśli aplikacja musi ograniczyć rejestracji do określonych tagów do określonych klientów (na przykład, jeśli znaczniki odpowiadają identyfikatory użytkowników), zaplecza aplikacji należy wykonać rejestracji. Aby uzyskać więcej informacji zobacz Zarządzanie rejestracją. Należy pamiętać, że w ten sposób aplikacja kliencka nie będzie miało bezpośredni dostęp do usługi Notification Hubs.

## <a name="security-claims"></a>Oświadczeń zabezpieczeń

Podobnie jak inne podmioty, operacje Centrum powiadomień są dozwolone dla trzech oświadczeń zabezpieczeń: Posłuchaj, wysyłanie i zarządzanie.

| Oświadczenie   | Opis                                          | Dozwolone operacje |
| ------- | ---------------------------------------------------- | ------------------ |
| Nasłuchuj  | Utwórz/zaktualizuj, Odczyt i usuwanie pojedynczego rejestracji | Utwórz/zaktualizuj rejestracji<br><br>Odczyt rejestracji<br><br>Odczyt wszystkich rejestracji dla dojścia<br><br>Usuwanie rejestracji |
| Wyślij    | Wysyłanie komunikatów do Centrum powiadomień                | Wyślij wiadomość |
| Zarządzanie  | CRUDs (w tym aktualizowanie poświadczenia systemu powiadomień platformy i kluczy zabezpieczeń) w usłudze Notification Hubs i odczytu rejestracji na podstawie tagów |Usługa tworzenia/aktualizacji/odczyt/usuwanie notification hubs<br><br>Odczyt rejestracji według tagu |

Usługa Notification Hubs akceptować oświadczeń przyznane przez Microsoft Azure Access Control tokenów i tokenów sygnatur wygenerowane z klucze współużytkowane są konfigurowane bezpośrednio w Centrum powiadomień.

Nie jest możliwe wysłanie powiadomienia do więcej niż jednej przestrzeni nazw. Przestrzenie nazw to kontener logiczny dla usługi notification hubs i nie są związane z wysyłaniem powiadomień.
Zasady dostępu na poziomie przestrzeni nazw (poświadczenia) może służyć do operacji na poziomie przestrzeni nazw, na przykład: wyświetlanie listy usługi notification hubs, tworzenie lub usuwanie usługi notification hubs, itp. Tylko zasady dostępu na poziomie koncentratora będzie można wysyłać powiadomienia.
