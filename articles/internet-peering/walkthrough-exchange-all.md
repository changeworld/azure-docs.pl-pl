---
title: Przewodnik po komunikacji równorzędnej z punktem wymiany
titleSuffix: Azure
description: Przewodnik po komunikacji równorzędnej z punktem wymiany
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775175"
---
# <a name="exchange-peering-walkthrough"></a>Przewodnik po komunikacji równorzędnej z punktem wymiany

W tej sekcji opisano kroki, które należy wykonać, aby skonfigurować komunikację równorzędną programu Exchange i zarządzać nią.

## <a name="create-an-exchange-peering"></a>Tworzenie komunikacji równorzędnej programu Exchange
> [!div class="mx-imgBorder"]
> ![Przepływ pracy komunikacji równorzędnej programu Exchange i stany połączeń](./media/exchange-peering.png)

Aby aprowizować komunikację równorzędną programu Exchange, należy wykonać następujące kroki:
1. Przejrzyj [zasady komunikacji równorzędnej](https://peering.azurewebsites.net/peering) firmy Microsoft, aby zrozumieć wymagania dotyczące komunikacji równorzędnej programu Exchange.
1. Znajdowanie identyfikatora lokalizacji komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft w [aplikacji PeeringDB](https://www.peeringdb.com/net/694)
1. Żądanie komunikacji równorzędnej programu Exchange dla lokalizacji komunikacji równorzędnej przy użyciu instrukcji w [tworzenie i modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu artykułu programu PowerShell,](howto-exchange-powershell.md) aby uzyskać więcej informacji.
1. Po przesłaniu żądania komunikacji równorzędnej firma Microsoft przejrzy żądanie i skontaktuje się z Tobą, jeśli jest to wymagane.
1. Po zatwierdzeniu stan połączenia zmienia się na Zatwierdzony
1. Konfigurowanie sesji BGP na końcu i powiadamianie firmy Microsoft
1. Będziemy aprowizować sesję Protokołu BGP za pomocą zasad DENY ALL i sprawdzać poprawność end-to-end.
1. Jeśli się powiedzie, otrzymasz powiadomienie, że stan połączenia komunikacji równorzędnej jest aktywny.
1. Ruch będzie następnie dozwolony przez nowe komunikacji równorzędnej.

Należy zauważyć, że stany połączeń nie należy mylić ze standardowymi stanami sesji [BGP.](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Konwertowanie starszej komunikacji równorzędnej z punktem wymiany na zasób platformy Azure
Aby przekonwertować starszą usługę Exchange w komunikacji równorzędnej na zasób platformy Azure, należy wykonać następujące kroki:
1. Postępuj zgodnie z instrukcjami w [obszarze Konwertuj starszą komunikację równorzędną programu Exchange na zasób platformy Azure](howto-legacy-exchange-powershell.md)
1. Po przesłaniu żądania konwersji firma Microsoft przejrzy żądanie i skontaktuje się z Tobą w razie potrzeby.
1. Po zatwierdzeniu zostanie wyświetlony stan komunikacji równorzędnej programu Exchange ze stanem połączenia jako aktywny.

## <a name="deprovision-exchange-peering"></a>Usuwanie obsługi równorzędnej programu Exchange
Skontaktuj się z [komunikacją równorzędność firmy Microsoft,](mailto:peering@microsoft.com) aby anulować komunikację równorzędność programu Exchange.

Gdy komunikacja równorzędna programu Exchange jest ustawiona na deprovision, zobaczysz stan połączenia jako **PendingRemove**

> [!NOTE]
> Jeśli uruchomisz polecenie cmdlet programu PowerShell, aby usunąć komunikację równorzędną programu Exchange, gdy stan połączenia jest Inicjowanie obsługi administracyjnejstarted lub Inicjowanie obsługi administracyjnejUpełnione operacja zakończy się niepowodzeniem.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [wymaganiach wstępnych do skonfigurowania komunikacji równorzędnej z firmą Microsoft](prerequisites.md).
