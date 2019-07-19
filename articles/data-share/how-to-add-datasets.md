---
title: Dodawanie zestawów danych do istniejącego udziału w wersji zapoznawczej usługi Azure Data Share
description: Dodawanie zestawów danych do istniejącego udziału
author: madams0013
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: t-maadam
ms.openlocfilehash: 01197d91277c59b58c5ab841dfc2abfb78be71de
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877287"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share-preview"></a>Jak dodać zestawy danych do istniejącego udziału w wersji zapoznawczej usługi Azure Data Share

W tym artykule wyjaśniono, jak dodać zestawy danych do istniejącego już udziału w usłudze Azure Data Share Preview. Pozwala to na udostępnianie większej ilości danych tym samym odbiorcom bez konieczności tworzenia nowego udziału.

Aby uzyskać informacje na temat dodawania zestawów danych podczas tworzenia udziału, zobacz samouczek [udostępnianie danych](share-your-data.md) .

## <a name="navigate-to-a-sent-data-share"></a>Przejdź do wysłanego udziału danych

W wersji zapoznawczej usługi Azure Data Share przejdź do wysłanego udziału i wybierz kartę **zestawy danych** . Kliknij przycisk **+ Dodaj zestawy danych** , aby dodać więcej zestawów danych.

![Dodaj zestawy danych](./media/how-to/how-to-add-datasets/add-datasets.png)

W panelu po prawej stronie wybierz typ zestawu danych, który chcesz dodać, a następnie kliknij przycisk **dalej**. Wybierz subskrypcję i grupę zasobów danych, które chcesz dodać. Za pomocą strzałek listy rozwijanej Znajdź, a następnie zaznacz pole wyboru obok danych do dodania.

![Dodaj zestawy danych](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Po kliknięciu przycisku **Dodaj zestawy**danych zestawy danych zostaną dodane do udziału. Uwaga: Aby można było zobaczyć nowe zestawy danych, musi być wyzwalane przez odbiorców. Jeśli ustawienia migawki zostały skonfigurowane, użytkownicy będą widzieli nowe zestawy danych po zakończeniu następnej zaplanowanej migawki. Bez skonfigurowanych ustawień migawek konsument musi ręcznie wyzwolić pełną lub przyrostową kopię danych w celu uzyskania aktualizacji. Aby uzyskać więcej informacji na temat migawek [, zobacz](terminology.md)snapshots.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [dodawania odbiorców do istniejącego udziału danych](how-to-add-recipients.md).