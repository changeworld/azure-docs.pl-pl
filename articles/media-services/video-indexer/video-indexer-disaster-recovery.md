---
title: Rejestrator wideo w pracy awaryjnej i odzyskiwania po awarii
titleSuffix: Azure Media Services
description: Dowiedz się, jak przejść w stan failover do dodatkowego konta indeksatora wideo, jeśli wystąpi awaria lub awaria regionalnego centrum danych.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 17c21900448fcb6d0a40fe5407f3b8bd62f9e3e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499610"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Rejestrator wideo w pracy awaryjnej i odzyskiwania po awarii

Indeksator wideo usługi Azure Media Services nie zapewnia natychmiastowego trybu failover usługi, jeśli występuje awaria lub awaria regionalnego centrum danych. W tym artykule wyjaśniono, jak skonfigurować środowisko pod kątem pracy awaryjnej, aby zapewnić optymalną dostępność dla aplikacji i zminimalizować czas odzyskiwania w przypadku awarii.

Zaleca się skonfigurowanie odzyskiwania po awarii (BCDR) ciągłości działania między parami regionalnymi, aby korzystać z zasad izolacji i dostępności platformy Azure. Aby uzyskać więcej informacji, zobacz [Sparowane regiony platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, zarejestruj się w ramach [bezpłatnej wersji próbnej platformy Azure.](https://azure.microsoft.com/free/)

## <a name="failover-to-a-secondary-account"></a>Przewijowanie awaryjne do konta pomocniczego

Aby zaimplementować BCDR, musisz mieć dwa konta indeksatora wideo do obsługi nadmiarowości.

1. Utwórz dwa konta indeksatora wideo połączone z platformą Azure (zobacz [Tworzenie konta indeksatora wideo).](connect-to-azure.md) Utwórz jedno konto dla regionu podstawowego, a drugie dla sparowanego regionu platformy Azure.
1. Jeśli występuje błąd w regionie podstawowym, przełącz się na indeksowanie przy użyciu konta pomocniczego.

> [!TIP]
> BcDR można zautomatyzować, konfigurując alerty dziennika aktywności dla powiadomień o kondycji usługi zgodnie z [Tworzenie alertów dziennika aktywności w powiadomieniach o usługach](../../service-health/alerts-activity-log-service-notifications.md).

Aby uzyskać informacje na temat korzystania z wielu dzierżaw, zobacz [Zarządzanie wieloma dzierżawcami](manage-multiple-tenants.md). Aby zaimplementować bcdr, wybierz jedną z tych dwóch opcji: [konto indeksatora wideo na dzierżawę](manage-multiple-tenants.md#video-indexer-account-per-tenant) lub [subskrypcję platformy Azure na dzierżawę.](manage-multiple-tenants.md#azure-subscription-per-tenant)

## <a name="next-steps"></a>Następne kroki

[Zarządzanie kontem indeksatora wideo połączonym z platformą Azure](manage-account-connected-to-azure.md).
