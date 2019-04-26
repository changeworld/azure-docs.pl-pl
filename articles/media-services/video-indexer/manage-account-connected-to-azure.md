---
title: Zarządzanie kontem indeksatora wideo
titlesuffix: Azure Media Services
description: W tym artykule przedstawiono sposób zarządzania konto usługi Video Indexer połączony z platformą Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 1515a026ae297a960f220a97449d2258c0b75e58
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553609"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Zarządzanie kontem Video Indexer połączony z platformą Azure

W tym artykule przedstawiono sposób zarządzania konta Video Indexer, który jest połączony z subskrypcją platformy Azure i konto usługi Azure Media Services.

> [!NOTE]
> Musisz być Video Indexer konto właściciela konta zmian w konfiguracji omówionych w tym temacie.

## <a name="prerequisites"></a>Wymagania wstępne

Połącz swoje konto usługi Video Indexer na platformie Azure, zgodnie z opisem w [połączony z platformą Azure](connect-to-azure.md). 

Upewnij się, że należy wykonać [wymagania wstępne](connect-to-azure.md#prerequisites) i przejrzyj [zagadnienia](connect-to-azure.md#considerations) w artykule.

## <a name="examine-account-settings"></a>Sprawdź ustawienia konta

W tej sekcji sprawdza, czy ustawienia konta usługi Video Indexer.

Aby wyświetlić ustawienia:

1. Kliknij ikonę użytkownika w prawym górnym rogu i wybierz pozycję **ustawienia**.

    ![Ustawienia](./media/manage-account-connected-to-azure/select-settings.png)

2. Na **ustawienia** wybierz opcję **konta** kartę.

Jeśli Twoje konto indeksatora wideo jest podłączony do platformy Azure, zobaczysz następujące czynności:

* Nazwa podstawowego konta usługi Azure Media Services.
* Liczba zadań uruchamiania i umieszczonych w kolejce.
* Liczba i typ przydzielonych jednostek zarezerwowanych.

Jeśli Twoje konto musi mieć pewnych zmian, zobaczysz istotne błędy i ostrzeżenia dotyczące konfiguracji konta na **ustawienia** strony. Komunikaty zawierają łącza do dokładne miejsca w witrynie Azure portal, w którym należy wprowadzić zmiany. Aby uzyskać więcej informacji, zobacz [błędy i ostrzeżenia](#errors-and-warnings) Poniższa sekcja.

## <a name="auto-scale-reserved-units"></a>Automatyczne skalowanie, zarezerwowane jednostki

**Ustawienia** strony pozwala na ustawienie skalowania automatycznego z nośnika zastrzeżonych jednostek. Jeśli ta opcja jest **na**, można przydzielić maksymalną liczbę jednostek żądania i pamiętaj, że Video Indexer zatrzymuje/Start (RUS) automatycznie. Po wybraniu tej opcji nie zapłacenia pieniędzy dodatkowy czas bezczynności, ale również nie czeka na zadania do wykonania przez długi czas, kiedy indeksowania duże obciążenie jest indeksowania.

Automatyczne skalowanie nie skaluje poniżej 1 RU lub powyżej limitu domyślnego konta usługi Media Services. Aby zwiększyć limit, Utwórz żądanie obsługi. Aby uzyskać informacje o limitach przydziałów i ograniczeń i sposobu otwierania biletu pomocy technicznej, zobacz [przydziały i ograniczenia](../../media-services/previous/media-services-quotas-and-limitations.md).

![Rejestrowanie](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Błędy i ostrzeżenia

Jeśli Twoje konto musi mieć pewnych zmian, zobaczysz istotne błędy i ostrzeżenia dotyczące konfiguracji konta na **ustawienia** strony. Komunikaty zawierają łącza do dokładne miejsca w witrynie Azure portal, w którym należy wprowadzić zmiany. Ta sekcja zawiera szczegółowe informacje o błędach i komunikaty ostrzegawcze.

* Event Grid

    Należy zarejestrować dostawcę zasobów EventGrid przy użyciu witryny Azure portal. W [witryny Azure portal](https://portal.azure.com/), przejdź do **subskrypcje** > [subskrypcja] > **ResourceProviders** > **Microsoft.EventGrid**. W przeciwnym razie w **zarejestrowanej** stan, kliknij przycisk **zarejestrować**. Może potrwać kilka minut, aby zarejestrować. 

* Punkt końcowy przesyłania strumieniowego

    Upewnij się, bazowego konta usługi Media Services ma domyślne **punkt końcowy przesyłania strumieniowego** w stanie uruchomionym. W przeciwnym razie nie można oglądać filmy wideo za pomocą tego konta usługi Media Services lub Video Indexer.

* Jednostki zarezerwowane multimediów 

    Należy przydzielić jednostki zarezerwowane multimediów zasobu usługi multimediów w celu wideo indeksu. Aby uzyskać optymalną wydajność indeksowania zaleca się przydzielenie co najmniej 10 jednostki zarezerwowane S3. Aby uzyskać informacje o cenach, zobacz sekcję Często zadawane pytania dotyczące [cennika usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/) strony.   

## <a name="next-steps"></a>Kolejne kroki

Mogą programowo współdziałać z konta wersji próbnej i/lub z kontami usługi Video Indexer, które są podłączone do platformy azure, postępując zgodnie z instrukcjami wyświetlanymi w: [Korzystanie z interfejsów API](video-indexer-use-apis.md).

Należy używać tego samego użytkownika usługi Azure AD, używane podczas nawiązywania połączenia z platformą Azure.
