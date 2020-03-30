---
title: Zmiana w miejscu wersji PROGRAMU SQL Server
description: Dowiedz się, jak zmienić wersję maszyny wirtualnej programu SQL Server na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: bd9622d3e34198402bc45e5d855d05b0c8e35eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605461"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Zmiana w miejscu wersji SQL Server na maszynie Wirtualnej platformy Azure

W tym artykule opisano sposób zmiany wersji programu SQL Server na maszynie wirtualnej systemu Windows na platformie Azure. 

Wersja programu SQL Server jest określana przez klucz produktu i jest określona podczas procesu instalacji przy użyciu nośnika instalacyjnego. Wersja określa, jakie [funkcje](/sql/sql-server/editions-and-components-of-sql-server-2017) są dostępne w produkcie SQL Server. Można zmienić wersję SQL Server edition za pomocą nośnika instalacyjnego i obniżyć na starszą wersję, aby zmniejszyć koszty lub uaktualnić, aby włączyć więcej funkcji.

Po wewnętrznej zmianie wersji programu SQL Server na maszynę wirtualną programu SQL Server należy zaktualizować właściwość programu SQL Server w witrynie Azure portal do celów rozliczeniowych. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać zmianę w miejscu wersji programu SQL Server, należy wykonać następujące czynności: 

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- Maszyna [wirtualna programu SQL Server w systemie Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) zarejestrowana u dostawcy zasobów maszyny [Wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- Instaluj nośnik z **żądaną edycją** programu SQL Server. Klienci korzystający z [pakietu Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) mogą uzyskać nośnik instalacyjny z Centrum [licencjonowania zbiorowego.](https://www.microsoft.com/Licensing/servicecenter/default.aspx) Klienci, którzy nie mają pakietu Software Assurance, mogą używać nośnika konfiguracyjnego z obrazu `C:\SQLServerFull`maszyny Wirtualnej programu Azure Marketplace SQL Server, który ma żądaną wersję (zazwyczaj znajduje się w ). 


## <a name="upgrade-an-edition"></a>Uaktualnianie wersji

> [!WARNING]
> Uaktualnienie wersji programu SQL Server spowoduje ponowne uruchomienie usługi dla programu SQL Server wraz z wszelkimi skojarzonymi usługami, takimi jak Analysis Services i R Services. 

Aby uaktualnić wersję programu SQL Server, należy uzyskać nośnik instalacyjny programu SQL Server dla żądanej wersji programu SQL Server, a następnie wykonaj następujące czynności:

1. Otwórz program Setup.exe z nośnika instalacyjnego programu SQL Server. 
1. Przejdź do **serwisu Konserwacja** i wybierz opcję **Uaktualnianie wersji.** 

   ![Wybór do uaktualniania wersji programu SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Wybierz **pozycję Dalej,** aż dojdziesz do strony **Gotowe do uaktualnienia wersji,** a następnie wybierz **pozycję Uaktualnij**. Okno konfiguracji może przestać odpowiadać na kilka minut, gdy zmiana zostanie zaa ustosunkowana. **Ukończona** strona potwierdzi, że uaktualnienie wersji zostało zakończone. 

Po uaktualnieniu wersji programu SQL Server zmodyfikuj właściwość wersji maszyny wirtualnej programu SQL Server w portalu Azure. Spowoduje to zaktualizowanie metadanych i rozliczeń skojarzonych z tą maszyną wirtualną.

## <a name="downgrade-an-edition"></a>Downgrade wersji

Aby obniżyć wersję programu SQL Server, należy całkowicie odinstalować program SQL Server i ponownie zainstalować go ponownie za pomocą żądanego nośnika instalacyjnego. 

> [!WARNING]
> Odinstalowanie programu SQL Server może spowodować dodatkowe przestoje. 

Wersję programu SQL Server można obniżyć, wykonując następujące kroki:

1. Tworzenie kopii zapasowych wszystkich baz danych, w tym systemowych baz danych. 
1. Przenieś systemowe bazy danych (wzorzec, model i msdb) do nowej lokalizacji. 
1. Całkowicie odinstaluj program SQL Server i wszystkie skojarzone usługi. 
1. Uruchom ponownie maszynę wirtualną. 
1. Zainstaluj program SQL Server przy użyciu nośnika z żądaną edycją programu SQL Server.
1. Zainstaluj najnowsze dodatki Service Pack i aktualizacje zbiorcze.  
1. Zastąp nowe bazy danych systemu, które zostały utworzone podczas instalacji, bazami danych systemu, które zostały wcześniej przeniesione do innej lokalizacji. 

Po zmianie wersji programu SQL Server zmodyfikuj właściwość wersji maszyny wirtualnej programu SQL Server w portalu Azure. Spowoduje to zaktualizowanie metadanych i rozliczeń skojarzonych z tą maszyną wirtualną.

## <a name="change-edition-in-portal"></a>Zmień wydanie w portalu 

Po zmianie wersji programu SQL Server przy użyciu nośnika instalacyjnego i zarejestrowaniu maszyny Wirtualnej programu SQL Server u [dostawcy zasobów maszyny Wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md)można następnie użyć portalu Azure do zmodyfikowania właściwości Edition maszyny Wirtualnej programu SQL Server do celów rozliczeniowych. Aby to zrobić, wykonaj następujące kroki: 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). 
1. Przejdź do zasobu maszyny wirtualnej programu SQL Server. 
1. W obszarze **Ustawienia**wybierz pozycję **Konfiguruj**. Następnie wybierz żądaną wersję programu SQL Server z listy rozwijanej w obszarze **Edycja**. 

   ![Zmienianie metadanych wersji](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Przejrzyj ostrzeżenie, które mówi, że należy najpierw zmienić wydanie programu SQL Server i że właściwość edition musi być zgodna z wydaniem programu SQL Server. 
1. Wybierz **zastosuj,** aby zastosować zmiany metadanych wersji. 


## <a name="remarks"></a>Uwagi

- Właściwość edition dla maszyny Wirtualnej programu SQL Server musi być zgodna z wydaniem wystąpienia programu SQL Server zainstalowanego dla wszystkich maszyn wirtualnych programu SQL Server, w tym zarówno typu licencji płatnych zgodnie z rzeczywistym i samodzielnym.
- Jeśli upuścisz zasób maszyny Wirtualnej programu SQL Server, powrócisz do ustawienia wersji zakodowanej obrazu.
- Możliwość zmiany wersji jest funkcją dostawcy zasobów maszyny Wirtualnej SQL. Wdrażanie obrazu portalu Azure Marketplace za pośrednictwem witryny Azure portal automatycznie rejestruje maszynę wirtualną programu SQL Server u dostawcy zasobów. Jednak klienci, którzy samodzielnie instalują program SQL Server, będą musieli ręcznie [zarejestrować swoją maszynę wirtualną programu SQL Server.](virtual-machines-windows-sql-register-with-resource-provider.md)
- Dodanie maszyny Wirtualnej programu SQL Server do zestawu dostępności wymaga ponownego utworzenia maszyny Wirtualnej. Wszystkie maszyny wirtualne dodane do zestawu dostępności zostaną ponownie dodane do wersji domyślnej, a wersja będzie musiała zostać ponownie zmodyfikowana.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Często zadawane pytania dotyczące programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Wskazówki dotyczące cen dla programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Informacje o wersji programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


