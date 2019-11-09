---
title: Zarządzanie kontem Video Indexer
titleSuffix: Azure Media Services
description: W tym artykule pokazano, jak zarządzać kontem Video Indexer połączonym z platformą Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: a8221cdcf7afe82ee969f645d7f459c69519711d
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838984"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Zarządzanie kontem Video Indexer połączonym z platformą Azure

W tym artykule pokazano, jak zarządzać kontem Video Indexer, które jest połączone z subskrypcją platformy Azure i kontem Azure Media Services.

> [!NOTE]
> Musisz być właścicielem konta Video Indexer, aby dostosować konfigurację konta omówione w tym temacie.

## <a name="prerequisites"></a>Wymagania wstępne

Połącz konto Video Indexer z platformą Azure zgodnie z opisem w artykule [połączono z platformą Azure](connect-to-azure.md). 

Upewnij się, że [spełniono wymagania wstępne](connect-to-azure.md#prerequisites) i zapoznaj się z [zagadnieniami](connect-to-azure.md#considerations) w artykule.

## <a name="examine-account-settings"></a>Sprawdzanie ustawień konta

Ta sekcja bada ustawienia konta Video Indexer.

Aby wyświetlić ustawienia:

1. Kliknij ikonę użytkownika w prawym górnym rogu, a następnie wybierz pozycję **Ustawienia**.

    ![Ustawienia](./media/manage-account-connected-to-azure/select-settings.png)

2. Na stronie **Ustawienia** wybierz kartę **konto** .

Jeśli konto indeksatora wideo jest połączone z platformą Azure, zobaczysz następujące kwestie:

* Nazwa podstawowego konta Azure Media Services.
* Liczba zadań indeksowania uruchomionych i umieszczonych w kolejce.
* Liczba i typ przydzielonej jednostki zarezerwowanej.

Jeśli Twoje konto wymaga wprowadzenia zmian, zostaną wyświetlone odpowiednie błędy i ostrzeżenia dotyczące konfiguracji konta na stronie **Ustawienia** . Komunikaty zawierają linki do dokładnych miejsc w Azure Portal, w których należy wprowadzić zmiany. Aby uzyskać więcej informacji, zobacz sekcję [błędy i ostrzeżenia](#errors-and-warnings) poniżej.

## <a name="auto-scale-reserved-units"></a>Jednostki zarezerwowane automatyczne skalowanie

Strona **Ustawienia** umożliwia ustawienie automatycznego skalowania jednostek zarezerwowanych multimediów (ru). Jeśli opcja jest **włączona**, można przydzielić maksymalną liczbę jednostek ru i upewnić się, że Video Indexer zatrzyma/uruchamia jednostek ru automatycznie. W przypadku tej opcji nie należy uiszczać dodatkowych opłat za czas bezczynności, ale również nie czekać na ukończenie zadań indeksowania, gdy obciążenie indeksowania jest wysokie.

Skalowanie automatyczne nie jest skalowane poniżej 1 RU ani powyżej domyślnego limitu konta Media Services. Aby zwiększyć limit, należy utworzyć żądanie obsługi. Aby uzyskać informacje na temat przydziałów i ograniczeń oraz sposobu otwierania biletu pomocy technicznej, zobacz [limity przydziału i ograniczenia](../../media-services/previous/media-services-quotas-and-limitations.md).

![Rejestrowanie](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Błędy i ostrzeżenia

Jeśli Twoje konto wymaga pewnych dostosowań, zobaczysz odpowiednie błędy i ostrzeżenia dotyczące konfiguracji konta na stronie **Ustawienia** . Komunikaty zawierają linki do dokładnych miejsc w Azure Portal, w których należy wprowadzić zmiany. Ta sekcja zawiera więcej szczegółów o komunikatach o błędach i ostrzeżeniach.

* Event Grid

    Należy zarejestrować dostawcę zasobów EventGrid za pomocą Azure Portal. W [Azure Portal](https://portal.azure.com/)przejdź do pozycji **subskrypcje** > [subskrypcja] > **ResourceProviders** > **Microsoft. EventGrid**. Jeśli nie jest w **zarejestrowanym** stanie, kliknij pozycję **zarejestruj**. Rejestracja może potrwać kilka minut. 

* Punkt końcowy przesyłania strumieniowego

    Upewnij się, że konto bazowe Media Services ma domyślny **punkt końcowy przesyłania strumieniowego** w stanie uruchomienia. W przeciwnym razie nie będzie można oglądać filmów wideo z tego konta Media Services ani Video Indexer.

* Jednostki zarezerwowane multimediów 

    Aby można było indeksować wideo, należy przydzielić jednostki zarezerwowane multimediów w ramach zasobu usługi multimediów. Aby zapewnić optymalną wydajność indeksowania, zaleca się przydzielenie co najmniej 10 jednostek zarezerwowanych S3. Aby uzyskać informacje o cenach, zapoznaj się z sekcją często zadawanych pytań na stronie [cennika Media Services](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="next-steps"></a>Następne kroki

Możesz programowo korzystać z konta próbnego i/lub z kontami Video Indexer, które są połączone z platformą Azure, postępując zgodnie z instrukcjami w temacie: [Korzystanie z interfejsów API](video-indexer-use-apis.md).

Należy używać tego samego użytkownika usługi Azure AD, który był używany podczas nawiązywania połączenia z platformą Azure.
