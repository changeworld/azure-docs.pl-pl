---
title: Dodawanie zestawów danych do istniejącego udziału danych platformy Azure
description: Dowiedz się, jak dodać zestawy danych do istniejącego udziału danych w usłudze Azure Data Share i udostępnić je tym samym adresatom.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490545"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Jak dodać zestawy danych do istniejącego udziału w udziale danych platformy Azure

W tym artykule wyjaśniono, jak dodać zestawy danych do istniejącego udziału danych przy użyciu usługi Azure Data Share. Dzięki temu można udostępniać więcej danych tym samym odbiorcom bez konieczności tworzenia nowego udziału.

Aby uzyskać informacje na temat dodawania zestawów danych podczas tworzenia udziału, zobacz samouczek [Udostępnianie danych.](share-your-data.md)

## <a name="navigate-to-a-sent-data-share"></a>Przechodzenie do folderu udostępniania danych wysłanych

W usłudze Azure Data Share przejdź do wysłanego udziału **+ Add Datasets** i wybierz kartę **Zestawy danych.**

![Dodawanie zestawów danych](./media/how-to/how-to-add-datasets/add-datasets.png)

W panelu po prawej stronie wybierz typ zestawu danych, który chcesz dodać, a następnie kliknij przycisk **Dalej**. Wybierz subskrypcję i grupę zasobów danych, które chcesz dodać. Korzystając ze strzałek listy rozwijanej, znajdź, a następnie zaznacz pole obok danych, które chcesz dodać.

![Dodawanie zestawów danych](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Po kliknięciu przycisku **Dodaj zestawy danych**zestawy danych zostaną dodane do udziału. Uwaga: Migawka musi być wyzwolona przez konsumentów, aby mogli zobaczyć nowe zestawy danych. Jeśli są skonfigurowane ustawienia migawki, konsumenci zobaczą nowe zestawy danych po zakończeniu następnej zaplanowanej migawki. Bez skonfigurowanych ustawień migawki konsument musi ręcznie wyzwolić pełną lub przyrostową kopię danych, aby otrzymywać aktualizacje. Aby uzyskać więcej informacji na temat migawek, zobacz [Migawki](terminology.md).

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [dodaniu adresatów do istniejącego udziału danych](how-to-add-recipients.md).