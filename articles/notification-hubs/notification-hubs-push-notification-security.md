---
title: Zabezpieczenia Notification Hubs
description: W tym temacie wyjaśniono zabezpieczenia centrów powiadomień platformy Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 753493100bbdb34255574656a47217560e2d321a
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213061"
---
# <a name="notification-hubs-security"></a>Zabezpieczenia Notification Hubs

## <a name="overview"></a>Przegląd

W tym temacie opisano model zabezpieczeń usługi Azure Notification Hubs.

## <a name="shared-access-signature-security-sas"></a>Zabezpieczenia sygnatur dostępu współdzielonego (SAS)

Notification Hubs implementuje schemat zabezpieczeń na poziomie jednostki o nazwie SAS (Sygnatura dostępu współdzielonego). Każda reguła zawiera nazwę, wartość klucza (wspólny klucz tajny) i zestaw praw, jak wyjaśniono w [oświadczeniach zabezpieczeń](#security-claims). Podczas tworzenia centrum powiadomień automatycznie tworzone są dwie reguły: jeden z prawami do **nasłuchiwania** (używane przez aplikację kliencką) i **jeden z** prawami (których zaplecze aplikacji).

W przypadku zarządzania rejestracją w aplikacjach klienckich, jeśli informacje wysyłane za pośrednictwem powiadomień nie są poufne (na przykład aktualizacje pogody), typowym sposobem uzyskiwania dostępu do centrum powiadomień jest nadanie wartości klucza reguły dostępu tylko do aplikacji klienckiej. i aby dać wartość klucza reguły pełen dostęp do zaplecza aplikacji.

Aplikacje nie powinny osadzać wartości klucza w aplikacjach klienckich ze sklepu Windows, zamiast tego aplikacja kliencka pobiera ją z zaplecza aplikacji podczas uruchamiania.

Klucz z dostępem do **nasłuchiwania** umożliwia aplikacji klienckiej rejestrację w celu dowolnych tagów. Jeśli aplikacja wymaga ograniczenia rejestracji do określonych klientów (na przykład gdy Tagi reprezentują identyfikatory użytkowników), zaplecze aplikacji musi wykonać rejestrację. Aby uzyskać więcej informacji, zobacz temat [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md). Należy zauważyć, że w ten sposób aplikacja kliencka nie będzie mieć bezpośredniego dostępu do Notification Hubs.

## <a name="security-claims"></a>Oświadczenia zabezpieczeń

Podobnie jak w przypadku innych jednostek, operacje centrum powiadomień są dozwolone dla trzech oświadczeń zabezpieczeń: **Słuchaj**, **wysyłania**i **zarządzania**.

| Oświadczenie   | Opis                                          | Dozwolone operacje |
| ------- | ---------------------------------------------------- | ------------------ |
| Nasłuchuj  | Tworzenie/aktualizowanie, odczytywanie i usuwanie pojedynczych rejestracji | Utwórz/zaktualizuj rejestrację<br><br>Odczytaj rejestrację<br><br>Odczytaj wszystkie rejestracje dla dojścia<br><br>Usuń rejestrację |
| Wyślij    | Wysyłanie komunikatów do centrum powiadomień                | Wyślij wiadomość |
| Zarządzanie  | CRUDs na Notification Hubs (w tym aktualizowanie poświadczeń PNS i kluczy zabezpieczeń) oraz odczytywanie rejestracji w oparciu o Tagi |Tworzenie/aktualizowanie/odczytywanie/usuwanie centrów powiadomień<br><br>Odczytaj rejestracje według tagu |

Notification Hubs akceptuje tokeny sygnatur wygenerowane z użyciem kluczy udostępnionych skonfigurowanych bezpośrednio w centrum powiadomień.

Wysłanie powiadomienia do więcej niż jednej przestrzeni nazw nie jest możliwe. Przestrzenie nazw są logicznymi kontenerami dla centrów powiadomień i nie są związane z wysyłaniem powiadomień.
Zasady dostępu na poziomie przestrzeni nazw (poświadczenia) mogą być używane dla operacji na poziomie przestrzeni nazw, na przykład: Wyświetlanie listy centrów powiadomień, tworzenie lub usuwanie centrów powiadomień itp. Tylko zasady dostępu na poziomie centrum umożliwiają wysyłanie powiadomień.
