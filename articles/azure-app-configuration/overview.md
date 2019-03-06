---
title: Co to jest usługa Azure App Configuration? | Microsoft Docs
description: Omówienie usługi Azure App Configuration.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 11dd91039bb352e86800982d0a294f82622a56fe
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885023"
---
# <a name="what-is-azure-app-configuration"></a>Co to jest usługa Azure App Configuration?

Azure App Configuration to usługa służąca do centralnego zarządzania ustawieniami aplikacji. Nowoczesne programy, zwłaszcza te działające w chmurze, zwykle mają wiele składników. Posiadanie ustawień konfiguracji w ramach tych składników może powodować występowanie błędów podczas wdrażania aplikacji, których diagnozowanie będzie bardzo skomplikowane. Usługa App Configuration pozwala na przechowywanie w jednym miejscu wszystkich ustawień aplikacji i zabezpieczanie ich.

## <a name="why-use-app-configuration"></a>Dlaczego warto korzystać z usługi App Configuration?

Aplikacje oparte na chmurze są często uruchamiane na wielu maszynach wirtualnych lub kontenerach w wielu regionach i korzystają z wielu usług zewnętrznych. Utworzenie aplikacji rozproszonej, która jest jednocześnie niezawodna i skalowalna, to duże wyzwanie. Powstały różne metodologie programowania, które pomagają programistom radzić sobie z rosnącą złożonością procesu tworzenia takich aplikacji. Na przykład w metodologii Twelve-Factor App (Dwanaście aspektów aplikacji) szczegółowo opisano wiele dobrze przetestowanych wzorców architektury i najlepszych rozwiązań, które mogą zostać zastosowane w przypadku aplikacji w chmurze. Jednym z kluczowych zaleceń z tego przewodnika jest oddzielenia konfiguracji od kodu. Oznacza to, że konfiguracja aplikacji, taka jak ustawienia, powinna być przechowywana poza warstwą wykonywalną i odczytywana ze środowiska uruchomieniowego lub źródła zewnętrznego.

Poniżej przedstawiono przykładowe typy aplikacji, które mogą korzystać z usługi App Configuration (może z nich jednak korzystać dowolna aplikacja):

* Mikrousługi na podstawie usług AKS i Service Fabric lub innej skonteneryzowane aplikacje wdrożone w co najmniej jednej lokalizacji geograficznej.
* Aplikacje bez użycia serwera, w tym aplikacje usługi Azure Functions, lub inne bezstanowe aplikacje do obliczeń sterowane zdarzeniami.
* Potok ciągłego wdrażania.

Usługa App Configuration zapewnia następujące korzyści:

* W pełni zarządzana usługa, którą można skonfigurować w ciągu kilku minut.
* Elastyczne reprezentacje kluczy i mapowania.
* Tagowanie za pomocą etykiet.
* Powtarzanie ustawień z punktu w czasie.
* Porównywanie dwóch zestawów konfiguracji w ramach niestandardowo zdefiniowanych wymiarów.
* Zwiększony poziom zabezpieczeń za pomocą tożsamości zarządzanych platformy Azure.
* Pełne szyfrowanie magazynowanych i przesyłanych danych.
* Natywna integracja z popularnymi platformami.

## <a name="how-to-use-app-configuration"></a>Jak używać usługi App Configuration

Najprostszym sposobem dodania magazynu konfiguracji aplikacji do aplikacji jest użycie biblioteki klienta udostępnianej przez firmę Microsoft. Poniższej przedstawiono najlepsze dostępne dla Ciebie metody w zależności od używanego języka programowania i platformy:

| Język programowania i platforma | Jak nawiązać połączenie |
|---|---|
| **.NET Core** i **ASP.NET Core** | Dostawca konfiguracji usługi App Configuration dla platformy .NET Core. |
| **.NET** i **ASP.NET** | Konstruktor konfiguracji usługi App Configuration dla platformy .NET. |
| **Java Spring** | Klient konfiguracji usługi App Configuration dla platformy Spring Cloud. |
| Inne | Interfejs API REST usługi App Configuration. |

## <a name="next-steps"></a>Następne kroki

* [Szybki start: Tworzenie aplikacji internetowej platformy ASP.NET](quickstart-aspnet-core-app.md)  
