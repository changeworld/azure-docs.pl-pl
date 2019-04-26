---
title: Dostosowywanie modelu osoby w Video Indexer — platformy Azure
titlesuffix: Azure Media Services
description: Ten artykuł zawiera omówienie modelu osoby w Video Indexer i sposobami ich dostosowywania.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 03/19/2019
ms.author: anzaman
ms.openlocfilehash: b491120639421d85d2fbb1a0efb2b6dd09ec1d4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553729"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Dostosowywanie modelu osoby w indeksatora wideo

Usługa Video Indexer obsługuje rozpoznawanie osobistości w filmach wideo. Funkcja rozpoznawania osobistości obejmuje około jeden milion twarze na podstawie źródła najczęściej żądanych danych, takich jak ZAUFANI Wikipedia i górnym specjaliści LinkedIn. Twarzy, które nie są rozpoznawane przez indeksator wideo nadal są wykrywane, ale są pozostawione bez nazwy. Klientów można tworzyć niestandardowe modele osoby i włączyć indeksatora wideo do rozpoznawania twarzy, które nie są rozpoznawane przez domyślny. Klienci mogą tworzyć modele te osoby, łącząc nazwiska osoby za pomocą plików obrazów twarzy osoby.  

Jeśli Twoje konto jest przeznaczony dla różnych przypadków użycia, możesz korzystać z możliwości tworzenia wielu modeli osoby na jednym koncie. Na przykład zawartość w ramach Twojego konta ma być posortowane według różnych kanałów, możesz utworzyć oddzielne model osoby dla każdego kanału. 

> [!NOTE]
> Każdy model osoby obsługuje maksymalnie 1 milion osób i każde konto ma ograniczenie do 50 modeli osoby. 

Po utworzeniu modelu, można użyć go, podając identyfikator modelu określonego modelu osoby podczas przekazywania indeksowania lub Indeksowanie filmu wideo. Szkolenia nowej twarzy, aby obejrzeć wideo, aktualizuje określonego modelu niestandardowego, który film wideo został skojarzony z. 

Jeśli nie potrzebujesz obsługi wielu modelu osoby, nie należy przypisywać osoby identyfikator modelu do swojego wideo podczas przekazywania indeksowania lub ponowne indeksowanie. W tym przypadku Video Indexer użyje domyślnego modelu osoby na Twoim koncie. 

Umożliwia Video Indexer witryny sieci Web do edytowania, twarze, które zostały wykryte w filmie wideo i zarządzanie wielu niestandardowych modeli osoby ze swojego konta, zgodnie z opisem w [dostosowania modelu osoba, za pomocą witryny sieci Web](customize-person-model-with-website.md) tematu. Możesz również użyć interfejsu API, zgodnie z opisem w [dostosowania modelu osoba, za pomocą interfejsów API](customize-person-model-with-api.md).