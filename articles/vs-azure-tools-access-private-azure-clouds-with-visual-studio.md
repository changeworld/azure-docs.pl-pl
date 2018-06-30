---
title: Uzyskiwanie dostępu do chmur prywatnych Azure z programem Visual Studio | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uzyskać dostęp do zasobów chmury prywatnej w programie Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: ghogen
ms.openlocfilehash: 0160ac6db2b92d5a30a19dd444d01a8558b3eed3
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "31792995"
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Uzyskiwanie dostępu do chmur prywatnych Azure z programem Visual Studio

Domyślnie program Visual Studio obsługuje punkty końcowe REST w chmurze Azure. W tym artykule Dowiedz się jak używać certyfikatu chmury prywatnej dostęp do interakcji z chmurą prywatną w programie Visual Studio.

1. W portalu Azure w ramach chmury prywatnej Pobierz swój plik ustawień publikowania, lub skontaktuj się z administratorem dla pliku ustawień publikowania. (Plik ma rozszerzenie `.publishsettings`.)

1. W programie Visual Studio **Eksploratora serwera**, kliknij prawym przyciskiem myszy **Azure** a następnie wybierz węzeł **zarządzanie i subskrypcje filtru**.

    ![Zarządzanie subskrypcjami polecenia](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. W **Zarządzanie subskrypcji platformy Microsoft Azure** okno dialogowe, wybierz opcję **certyfikaty** , a następnie wybierz **importu**.

    ![Importowanie certyfikatów Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. W **importu subskrypcji platformy Microsoft Azure** okno dialogowe, wybierz opcję **Przeglądaj**.

    ![Przeglądaj przycisk w oknie dialogowym Import subskrypcji platformy Microsoft Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. W **Otwórz** okno dialogowe, przejdź do katalogu, w której zapisano plik ustawień publikowania, wybierz plik, a następnie wybierz **Otwórz**.

    ![Wybierz plik ustawień publikowania](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. Gdy zwrócony do **importu subskrypcji platformy Microsoft Azure** okno dialogowe, wybierz opcję **importu**.

    ![Importowanie pliku ustawień publikowania](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    Certyfikaty są zaimportowane z pliku ustawień publikowania do programu Visual Studio, a teraz pozwala na interakcję z zasobami w chmurze prywatnej.

