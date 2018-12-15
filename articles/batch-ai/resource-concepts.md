---
title: O zasobach usługi Batch AI — Azure | Dokumentacja firmy Microsoft
description: Przegląd obszarów roboczych, klastry, serwery plików, eksperymenty i zadania usługi Batch AI w systemie Microsoft Azure.
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/15/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 4454448809411c6390bc1f65c33b4abe76094ded
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407749"
---
# <a name="overview-of-resources-in-batch-ai"></a>Przegląd zasobów usługi Batch AI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Przy pierwszym uruchomieniu przy użyciu usługi Batch AI, należy zrozumieć dostępne zasoby usługi Batch AI. Z innymi usługami Azure podczas tworzenia zasobów usługi Batch AI na platformie Azure co najmniej jeden *grup zasobów*. Tworzenie usługi Batch AI *obszary robocze* w grupie zasobów. Każdy obszar roboczy zawiera usługi Batch AI *klastrów*, *serwery plików*, i *eksperymentów*. Eksperymentu usługi Batch AI hermetyzuje grupę *zadań*.

Na poniższej ilustracji przedstawiono przykład hierarchii zasobów usługi Batch AI. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

Poniższe sekcje przejść do bardziej szczegółowo o zasobach usługi Batch AI.

## <a name="workspace"></a>Obszar roboczy

Obszar roboczy w usługi Batch AI to kolekcja najwyższego poziomu w pozostałej części zasobów usługi Batch AI. Obszary robocze ułatwiają oddzielenie pracy należących do różnych grup lub projektów. Na przykład utworzyć programowania i testów obszaru roboczego.

## <a name="cluster"></a>Klaster

W klastrze na platformie usługi Batch AI zawiera zasoby obliczeniowe dla uruchomionych zadań. Wszystkie węzły w klastrze mają ten sam rozmiar maszyny Wirtualnej i obrazu systemu operacyjnego. Usługa Batch AI oferują wiele opcji tworzenia klastrów, które są dostosowywane do różnych potrzeb. Zazwyczaj należy skonfigurować innego klastra dla każdej kategorii mocy obliczeniowej, potrzebne do ukończenia projektu. Skalę, jakiej klastry usługi Batch AI w górę i w dół na podstawie zapotrzebowania i budżetu. Aby uzyskać więcej informacji, zobacz [działają z klastrami usługi Batch AI](clusters.md).

## <a name="file-server"></a>Serwer plików

Opcjonalnie można utworzyć serwer plików w usługi Batch AI do przechowywania danych, skrypty, szkolenia i dzienników wyjściowych. Serwer plików usługi Batch AI to zarządzane NFS jednym węzłem, które mogą być automatycznie instalowane w węzłach klastra, aby zapewnić łatwy i centralnie dostępne do przechowywania zadań. W większości przypadków serwer tylko jeden plik jest potrzebny w obszarze roboczym, i możesz oddzielić danych zadania szkolenia w różnych katalogach. W przypadku systemu plików NFS nie jest odpowiednie dla obciążeń, usługa Batch AI obsługuje inne opcje magazynu, w tym [usługi Azure Storage](use-azure-storage.md) lub rozwiązaniami niestandardowymi, takich jak system plików Gluster lub Lustre.

## <a name="experiment"></a>Eksperyment

Eksperyment grupy to zbiór powiązane zadania, które zapytania i zarządzać razem. Każdy obszar roboczy może mieć licznych eksperymentów, gdzie każdy eksperymentu próbuje rozwiązać jednego konkretnego problemu.

## <a name="job"></a>Zadanie

Zadanie jest pojedynczego zadania lub skryptu, który ma zostać wykonana, na przykład aby szkolenie modeli uczenia głębokiego. Każde zadanie wykonuje określonego skryptu w jednym klastrze w obszarze roboczym. (Skrypt, mogą być przechowywane na serwerze plików usługi Batch AI lub innego rozwiązania do magazynowania). Każde zadanie usługi Batch AI ma typ framework skojarzone z nią: TensorFlow, Horovod, CNTK, Caffe, Caffe2, pyTorch, Chainer, niestandardowe MPI lub niestandardowy. Dla każdej struktury usługi Batch AI konfiguruje wymaganą infrastrukturę i zarządza procesami zadania. Każdego doświadczenia mogą mieć wiele zadań, które są podobne, oprócz pewne zmiany w różnych parametrów.

## <a name="next-steps"></a>Kolejne kroki

* Uruchamianie pierwszej [zadania szkoleniowego Batch AI](quickstart-tensorflow-training-cli.md).

* Sprawdź przykładowe [przepisy na szkolenie](https://github.com/Azure/BatchAI/tree/master/recipes) dla różnych platform.