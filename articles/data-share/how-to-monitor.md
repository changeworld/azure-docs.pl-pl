---
title: Jak monitorować udziału danych platformy Azure w wersji zapoznawczej
description: Jak monitorować udziału danych platformy Azure w wersji zapoznawczej
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 869c1ed41d7f78df184461bc1d8cab6c6eb8d426
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789100"
---
# <a name="monitor-azure-data-share-preview"></a>Monitor udziału danych platformy Azure w wersji zapoznawczej 

W tym artykule wyjaśniono, jak można monitorować swoje udziały danych przy użyciu udziału danych platformy Azure w wersji zapoznawczej. Jako dostawca danych jest możliwe monitorowanie różnych aspektów danych udostępnianie relacji. Szczegóły, takie jak czy klientów danych zaakceptowano zaproszenie do udziału danych także tego, czy masz utworzono subskrypcję udziału i zacząć korzystanie z Twoich danych będą dostępne do monitorowania. 

Jako użytkownik danych można monitorować migawki, które zostało wyzwolone do subskrypcji platformy Azure. 

## <a name="monitor-invitation-status"></a>Monitor stanu zaproszenia

Widok stanu zaproszeń udostępniania danych, przechodząc do udziałów wysłane -> zaproszeń. 

![Stan zaproszenia](./media/invitation-status.png "stan zaproszenia") 

Istnieją trzy stany, może być zaproszenia:

* Oczekiwanie — odbiorcy udostępniania danych nie zaakceptował jeszcze zaproszenia.
* Zaakceptowano - odbiorcy udostępniania danych zaakceptował zaproszenia.
* Odrzucony — odbiorcy udostępniania danych odrzuciła zaproszenia.

> [!IMPORTANT]
> Jeśli usuniesz zaproszenie po zostało już zaakceptowane, nie jest odpowiednikiem odbieranie prawa dostępu. Jeśli chcesz zatrzymać przyszłych migawek kopiowaniu do konta magazynu danych klientów, należy odwołać dostęp za pośrednictwem *udostępnianie subskrypcje* kartę. 

## <a name="monitor-share-subscriptions"></a>Monitor udziału subskrypcji

Wyświetl stan swoich subskrypcji udziału, przechodząc do udziałów wysyłane -> Subskrypcje udostępniania. Zapewni to szczegółowe informacje o aktywne subskrypcje utworzone przez użytkowników danych po zaakceptowaniu zaproszenia. Możesz zatrzymać przyszłe aktualizacje danych konsumentów, wybierając subskrypcję udziału i wybierając *odwołać*. 

## <a name="snapshot-history"></a>Migawki historii 

Na karcie Historia jest możliwe wyświetlić migawki, które zostały skopiowane do konsumenta danych dzierżawy w. Jesteś w stanie monitorować częstotliwość i czas trwania każdego interwał tworzenia migawki. 

![Migawki historii](./media/sent-shares.png "migawek historii") 

Można wyświetlić więcej szczegółów na temat każdej migawki uruchomić, klikając w dniu rozpoczęcia przebiegu. 

## <a name="next-steps"></a>Następne kroki 

Dowiedz się więcej o [terminologii udziału danych platformy Azure](terminology.md)