---
title: Przewodnik po bezpośredniej komunikacji równorzędnej
titleSuffix: Azure
description: Przewodnik po bezpośredniej komunikacji równorzędnej
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d88fcfc4d3e073bf544f2ca0f4d01dbe305b45da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775513"
---
# <a name="direct-peering-walkthrough"></a>Przewodnik po bezpośredniej komunikacji równorzędnej

W tej sekcji opisano kroki, które należy wykonać, aby skonfigurować komunikację bezpośrednią komunikacji równorzędnej i zarządzać nią.

## <a name="create-a-direct-peering"></a>Tworzenie komunikacji równorzędnej bezpośredniej
> [!div class="mx-imgBorder"]
> ![Bezpośredni przepływ pracy komunikacji równorzędnej i stany połączenia](./media/direct-peering.png)

Aby aprowizować komunikację równorzędną direct należy wykonać następujące kroki:
1. Przejrzyj [zasady komunikacji równorzędnej](https://peering.azurewebsites.net/peering) firmy Microsoft, aby zrozumieć wymagania dotyczące komunikacji bezpośredniej komunikacji równorzędnej.
1. Postępuj zgodnie z instrukcjami w [Tworzenie lub modyfikowanie direct komunikacji równorzędnej,](howto-direct-powershell.md) aby przesłać żądanie komunikacji równorzędnej.
1. Po przesłaniu żądania komunikacji równorzędnej firma Microsoft skontaktuje się z użytkownikiem przy użyciu zarejestrowanego adresu e-mail użytkownika w celu przekazania usługi LOA (Letter of Authorization) lub w celu uzyskania innych informacji.
1. Po zatwierdzeniu żądania komunikacji równorzędnej stan połączenia zmienia się na inicjowanie obsługi administracyjnejStarted.
1. Musisz:
    1. kompletne okablowanie zgodnie z LOA
    1. (opcjonalnie) wykonać test łącza za pomocą 169.254.0.0/16
    1. skonfigurować sesję BGP, a następnie powiadomić nas.
1. Microsoft przepisów sesji BGP z odmów wszystkich zasad i sprawdź poprawność end-to-end.
1. Jeśli się powiedzie, otrzymasz powiadomienie, że stan połączenia komunikacji równorzędnej jest aktywny.
1. Ruch będzie następnie dozwolony przez nowe komunikacji równorzędnej.

Należy zauważyć, że stany połączeń nie należy mylić ze standardowymi stanami sesji [BGP.](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure
Aby przekonwertować starsze bezpośrednie komunikacji równorzędnej do zasobu platformy Azure, należy wykonać następujące kroki:
1. Postępuj zgodnie z instrukcjami w [obszarze Konwertuj starszy bezpośrednią komunikację równorzędną na zasób platformy Azure](howto-legacy-direct-powershell.md)
1. Po przesłaniu żądania konwersji firma Microsoft przejrzy żądanie i skontaktuje się z Tobą w razie potrzeby.
1. Po zatwierdzeniu zostanie wyświetlony bezpośredni element równorzędny ze stanem połączenia jako Aktywny.

## <a name="deprovision-direct-peering"></a>Usuwanie obsługi równorzędnej bezpośredniej obsługi administracyjnej
Skontaktuj się z [zespołem komunikacji równorzędnej firmy Microsoft,](mailto:peering@microsoft.com) aby anulować komunikację równorzędność bezpośrednią.

Gdy direct peering jest ustawiony na deprovision, zobaczysz stan połączenia jako **PendingRemove**

> [!NOTE]
> Jeśli uruchomisz polecenie cmdlet programu PowerShell, aby usunąć bezpośrednie komunikacji równorzędnej, gdy connectionstate jest inicjowanie obsługi administracyjnejstarted lub inicjowania obsługi inicjowaniaZupełnione operacja zakończy się niepowodzeniem.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [wymaganiach wstępnych do skonfigurowania komunikacji równorzędnej z firmą Microsoft](prerequisites.md).
