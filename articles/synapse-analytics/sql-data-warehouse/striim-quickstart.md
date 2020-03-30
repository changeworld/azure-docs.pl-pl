---
title: Szybki start Striim
description: Szybkie rozpoczęcie pracy dzięki usługom Striim i usługi Azure SQL Data Warehouse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e963d62df8d2c416726852ee3d46daf1cd22936e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349939"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Striim Azure SQL DW Marketplace — przewodnik po instalacji

Ten przewodnik Szybki start zakłada, że masz już istniejące wystąpienie usługi SQL Data Warehouse.

Wyszukaj rozwiązanie Striim w portalu Azure Marketplace i wybierz opcję Striim for Data Integration to SQL Data Warehouse (Staged) 

![Instalowanie striim][install]

Konfigurowanie maszyny Wirtualnej Striim z określonymi właściwościami, odnoś się od nazwy klastra Striim, hasła i hasła administratora

![Konfigurowanie striim][configure]

Po wdrożeniu \<kliknij nazwę maszyny Wirtualnej> masternode w witrynie Azure portal, kliknij pozycję Połącz i skopiuj konto logowania przy użyciu konta lokalnego maszyny Wirtualnej 

![Łączenie striim z magazynem danych SQL][connect]

Pobierz sqljdbc42.jar <https://www.microsoft.com/en-us/download/details.aspx?id=54671> z lokalnego komputera. 

Otwórz okno wiersza polecenia i zmień katalogi w miejsce, w którym pobrano słoik JDBC. SCP plik jar do maszyny Wirtualnej Striim, uzyskanie adresu i hasła z witryny Azure portal

![Kopiowanie pliku jar do maszyny Wirtualnej][copy-jar]

Otwórz inne okno wiersza polecenia lub użyj narzędzia ssh, aby ssh do klastra Striim

![SSH do klastra][ssh]

Wykonaj następujące polecenia, aby przenieść plik jar JDBC do katalogu lib striim i uruchomić i zatrzymać serwer.

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. systemctl stop striim-node
   5. systemctl stop striim-dbms
   6. systemctl start striim-dbms
   7. systemctl start striim-node

![Uruchamianie klastra Striim][start-striim]

Teraz otwórz ulubioną przeglądarkę \<i przejdź do nazwy DNS>:9080

![Przechodzenie do ekranu logowania][navigate]

Zaloguj się przy użyciu nazwy użytkownika i hasła skonfigurowane w witrynie Azure portal, wybierz preferowanego kreatora, aby rozpocząć, lub przejdź do strony Aplikacje, aby rozpocząć korzystanie z interfejsu użytkownika przeciągania i upuszczania

![Zaloguj się przy użyciu poświadczeń serwera][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
