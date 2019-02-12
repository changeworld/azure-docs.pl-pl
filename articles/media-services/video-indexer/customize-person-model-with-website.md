---
title: Umożliwia dostosowywanie modelu osoby — Azure Video Indexer witryny sieci Web
titlesuffix: Azure Media Services
description: W tym artykule pokazano, jak dostosować model osoby z witryną internetową Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 29c32126a5bbe792dd6fe88ae189e09c511d2a22
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997071"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Dostosuj model osoby, z witryną internetową indeksatora wideo

Usługa Video Indexer obsługuje wykrywanie twarzy oraz rozpoznawanie osobistości zawartości wideo. Funkcja rozpoznawania osobistości obejmuje około jeden milion twarze na podstawie źródła najczęściej żądanych danych, takich jak ZAUFANI Wikipedia i górnym specjaliści LinkedIn. Wykryto twarzy, które nie są rozpoznawane przez funkcję rozpoznawania osobistości; jednak są pozostawione bez nazwy. Po przekazywania filmu wideo do indeksatora wideo i uzyskuj wyniki z powrotem, może Przejdź wstecz i nazwij twarzy, które nie zostały rozpoznane. Gdy etykiety twarz o nazwie twarzy i nazwy poproś o dodanie Cię do swojego konta osoby modelu. Indeksator wideo następnie rozpozna twarzach przyszłych filmów wideo i ostatnich filmów wideo.

Video Indexer witryny sieci Web służy do edytowania twarzy, które zostały wykryte w filmach wideo, zgodnie z opisem w tym temacie. Możesz również użyć interfejsu API, zgodnie z opisem w [Dostosuj osoby modelu przy użyciu interfejsów API](customize-person-model-with-api.md).

## <a name="edit-a-face"></a>Edytuj twarzy

> [!NOTE]
> Nazwy są unikatowe dla w modelach osoby, w przypadku, więc jeśli nazwie dwa różne twarzy taką samą nazwę, Video Indexer widoków powierzchnie jako ta sama osoba i zbieżna dla wartości, ich po ponownego poindeksowania danych wideo. Jeśli widzisz, że indeksator wideo Wykryto wiele wystąpień różnych tego samego twarzy, nadaj im takiej samej nazwie i ponowna indeksacja filmu wideo.
> Możesz zaktualizować krój Video Indexer, który został rozpoznany jako osobistości z nową nazwą. Nowa nazwa nadana ma pierwszeństwo rozpoznawania osobistości wbudowanych.

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
2. Wyszukiwanie wideo, który chcesz wyświetlić i edytować na Twoim koncie.
3. Aby edytować twarzy w wideo, przejdź do **Insights** kartę, a następnie kliknij ikonę ołówka w prawym górnym rogu okna.

    ![Dostosuj model osoby](./media/customize-face-model/customize-face-model.png)

4. Kliknij dowolny wykryte twarze i zmień ich nazwy "Nieznany #X" (lub nazwę, która wcześniej została przypisana do rozpoznawania twarzy).
5. Po wpisaniu nową nazwę, kliknij ikonę znacznika wyboru obok nowej nazwy. Spowoduje zapisanie nowej nazwy i rozpoznaje i nazwę wszystkich wystąpień tego twarzy w innych aktualnych filmów wideo i w przyszłości filmów wideo, które można przekazać. Rozpoznawanie twarzy w Twojej bieżącej wideo może trochę potrwać zaczęły obowiązywać, ponieważ jest to proces usługi batch. 

    ![Zapisywanie aktualizacji](./media/customize-face-model/save-update.png)

## <a name="delete-a-face"></a>Usuwanie twarzy

Aby usunąć twarz wykryte w wideo, przejdź do **Insights** okienku i kliknij ikonę ołówka w prawym górnym rogu okienka. Kliknij przycisk **Usuń** opcji pod nazwą powierzchni. Spowoduje to usunięcie które wykryte twarzy z filmu wideo. Twarz zostanie wykryta w innych filmach wideo, w których są wyświetlane, ale możesz usunąć twarz z pliki wideo, a także po zostały zindeksowane. Twarz również będą nadal istnieć w modelu osoby Twoje konto, jeśli nazwał ją przed jego usunięciem.

## <a name="next-steps"></a>Kolejne kroki

[Dostosuj model osoby za pomocą interfejsów API](customize-person-model-with-api.md)
