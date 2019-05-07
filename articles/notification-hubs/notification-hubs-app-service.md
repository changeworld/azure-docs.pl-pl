---
title: Integracja z usługą App Service Mobile Apps
description: Dowiedz się, jak usługa Azure Notification Hubs współpracuje z usługą Azure App Service Mobile Apps.
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: bb39949bc21ece819b7ac6e279390f8bdb0ee00a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158525"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integracja z usługą App Service Mobile Apps

> [!NOTE]
> Firma Microsoft dba o pełnej obsłudze usługi Azure App Service Mobile Apps, takie jak obsługa najnowszej wersji systemu operacyjnego, poprawki i udoskonalenia dokumentacji i przeglądy społeczności żądania Ściągnięcia. Należy pamiętać, że zespół pracujący nad produktem nie jest obecnie inwestowanie w dowolnej nowej pracy nad funkcjami usługi Azure Mobile Apps. Dziękujemy za zdecydowanie kod wniesiony przez społeczność do wszystkich obszarów usługi Azure Mobile Apps.

W celu umożliwienia bezproblemowej i jednorodnej obsługi we wszystkich usługach Azure funkcja [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) ma wbudowaną obsługę powiadomień wypychanych przy użyciu usługi Notification Hubs. Funkcja [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) oferuje wysoce skalowalną, globalnie dostępną platformę tworzenia aplikacji mobilnych dla deweloperów w przedsiębiorstwach i integratorów systemów. Platforma ta oferuje bogaty zestaw funkcji dla deweloperów aplikacji mobilnych.

Deweloperzy aplikacji mobilnych mogą korzystać z usługi Notification Hubs przy użyciu następującego przepływu pracy:

1. Pobranie dojścia do urządzenia w systemie powiadomień platformy
2. Rejestracja urządzenia w usłudze Notification Hubs przy użyciu wygodnego interfejsu API rejestracji w ramach zestawu SDK klienta funkcji Mobile Apps

    > [!NOTE]
    > Zauważ, że usługa Mobile Apps usuwa wszystkie tagi rejestracji ze względów bezpieczeństwa. Pracuj z usługą Notification Hubs bezpośrednio z zaplecza, aby skojarzyć tagi z urządzeniami.

3. Wysyłanie powiadomień z zaplecza aplikacji przy użyciu usługi Notification Hubs

Oto niektóre udogodnienia dla deweloperów wynikające z tej integracji:

- **Zestawy SDK klienta usługi Mobile Apps**: Te wieloplatformowe zestawy SDK zapewniają proste interfejsy API do rejestracji i komunikacji z centrum powiadomień automatycznie skojarzone z aplikacją mobilną. Deweloperzy nie muszą odnajdywać poświadczeń usługi Notification Hubs i pracować przy użyciu dodatkowych usług.
  - *Wypychanie do użytkownika*: Zestawy SDK automatycznie dodają tagi dla danego urządzenia przy użyciu identyfikatora uwierzytelnionego użytkownika usługi Mobile Apps w celu umożliwienia scenariusza wypychania do użytkownika.
  - *Wypychanie do urządzenia*: Zestawy SDK automatycznie używają identyfikatora instalacji usługi Mobile Apps jako identyfikatora GUID do rejestracji w usłudze Notification Hubs, oszczędzając deweloperom pracy związanej z obsługą identyfikatorów GUID wielu usług.
- **Model instalacji**: Usługa Mobile Apps współpracuje z najnowszym modelem wypychania usługi Notification Hubs w celu reprezentowania wszystkich właściwości wypychania skojarzonych z urządzeniem w instalacji JSON, które są zgodne z usługami powiadomień wypychanych i łatwe w użyciu.
- **Elastyczność**: Deweloperzy mogą zawsze pracować bezpośrednio za pomocą usługi Notification Hubs nawet po integracji.
- **Zintegrowane środowisko pracy w witrynie [Azure Portal](https://portal.azure.com)**: Wypychanie jako możliwość ma wizualną reprezentację w usłudze Mobile Apps, a deweloperzy mogą z łatwością pracować przy użyciu skojarzonego centrum powiadomień za pomocą usługi Mobile Apps.
