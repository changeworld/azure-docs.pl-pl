---
title: Rozwiązywanie problemów z wdrażaniem usługi chmury | Dokumentacja firmy Microsoft
description: Istnieje kilka typowych problemów, które mogą napotkać podczas wdrażania usługi w chmurze na platformie Azure. Ten artykuł zawiera niektóre z nich rozwiązania.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 3c56a5750c9f8a6c59ea07c01c101f358331174b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2017
ms.locfileid: "26408183"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Rozwiązywanie problemów z wdrażaniem usługi chmury
Podczas wdrażania pakietu aplikacji usług chmury Azure, można uzyskać informacji o wdrażaniu z **właściwości** okienku w portalu Azure. Szczegółowe informacje można użyć w tym okienku, ułatwiające rozwiązywanie problemów z usługą w chmurze, a następnie przekaż te informacje do obsługi Azure podczas otwierania nowe żądanie pomocy technicznej.

Można znaleźć **właściwości** okienko w następujący sposób:

* W portalu Azure kliknij wdrożenia usługi w chmurze, kliknij przycisk **wszystkie ustawienia**, a następnie kliknij przycisk **właściwości**.

> [!NOTE]
> Możesz skopiować zawartość **właściwości** okienko do Schowka, klikając ikonę w prawym górnym rogu okienka.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problem: nie można uzyskać dostęp do witryny sieci Web, ale Moje wdrożenie zostanie uruchomione i wszystkich wystąpień ról są gotowe
Łącze adres URL witryny sieci Web wyświetlana w portalu nie ma portu. Domyślny port dla witryn sieci Web to 80. Jeśli aplikacja jest skonfigurowana do uruchamiania w innego portu, należy dodać poprawny numer portu do adresu URL podczas uzyskiwania dostępu do witryny sieci Web.

1. W portalu Azure kliknij wdrożenia usługi w chmurze.
2. W **właściwości** okienku portalu Azure, sprawdź porty dla wystąpień roli (w obszarze **danych wejściowych punktów końcowych**).
3. Jeśli port nie jest 80, należy dodać wartość poprawnego portu do adresu URL, gdy uzyskujesz dostęp do aplikacji. Aby określić port inny niż domyślny, wpisz adres URL, z dwukropkiem (:) i numer portu, nie może zawierać spacji.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problem: Moje wystąpień ról poddanych recyklingowi bez żadnego działania do mnie
Naprawianie usługi odbywa się automatycznie, gdy Azure wykryje problem węzłów i w związku z tym przenosi wystąpień roli do nowych węzłów. W takim przypadku można napotkać odtwarzania automatycznie wystąpienia roli. Aby dowiedzieć się, jeśli naprawą usługi wystąpił:

1. W portalu Azure kliknij wdrożenia usługi w chmurze.
2. W **właściwości** okienku portalu Azure, zapoznaj się z informacjami i określenia, czy naprawą usługi wystąpiły w czasie obserwowanych ról odtwarzania.

Role również odtwarzana około raz w miesiącu podczas systemu operacyjnego hosta i aktualizacje systemu operacyjnego gościa.  
Aby uzyskać więcej informacji, zobacz w blogu [roli wystąpienia uruchamia ponownie z powodu uaktualnienia systemu operacyjnego](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problem: nie można wykonać wymiany wirtualnych adresów IP i komunikat o błędzie
Wymiany wirtualnych adresów IP jest niedozwolona, jeśli aktualizacja wdrożenia jest w toku. Wdrożenia aktualizacji mogą być wykonywane automatycznie po:

* Nowy system operacyjny gościa jest dostępna i skonfigurowane aktualizacje automatyczne.
* Naprawianie usługi występuje.

Aby dowiedzieć się, jeśli aktualizacje automatyczne uniemożliwia podczas wymiany wirtualnych adresów IP:

1. W portalu Azure kliknij wdrożenia usługi w chmurze.
2. W **właściwości** okienku portalu Azure, sprawdź wartość **stan**. Jeśli jest **gotowe**, następnie sprawdź **Ostatnia operacja** aby zobaczyć, jeśli jeden ostatnio wystąpił które mogą uniemożliwić wymiany wirtualnych adresów IP.
3. Powtórz kroki 1 i 2 w przypadku wdrożenia produkcyjnego.
4. Jeśli aktualizacje automatyczne jest w toku, zaczekaj na jego zakończenie przed próbujące przeprowadzić wymiany wirtualnych adresów IP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problem: Wystąpienie roli jest pętli między rozpoczęte, inicjowanie zajęty i zatrzymane
Ten stan może wskazywać na problem z kodem, pakietem lub plikiem konfiguracyjnym aplikacji. W takim przypadku można wyświetlić stan zmiana co kilka minut i portalu Azure może wyglądać mniej więcej tak powiedzieć **odtwarzanie**, **zajęty**, lub **inicjowanie**. Oznacza to, że jest wystąpiły problemy z aplikacją, która może być utrzymywanie uruchomienie wystąpienia roli.

Aby uzyskać więcej informacji na temat rozwiązywania tego problemu, zobacz we wpisie blogu [dane diagnostyczne obliczeniowe PaaS Azure](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) i [typowe problemy powodujące odtwarzanie ról](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problem: Moja aplikacja przestała działać
1. W portalu Azure kliknij wystąpienia roli.
2. W **właściwości** okienku portalu Azure, należy wziąć pod uwagę następujące warunki do rozwiązania problemu:
   * Jeśli ostatnio zatrzymał wystąpienie roli (można sprawdzić wartość **liczba przerwań**), wdrożenie może aktualizować. Poczekaj, aby sprawdzić, czy wystąpienia roli wznawia działanie samodzielnie.
   * W przypadku wystąpienia roli **zajęty**, sprawdź, czy kod aplikacji [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) zdarzenie jest obsługiwane. Może być konieczne, Dodaj lub Usuń kod obsługujący to zdarzenie.
   * Przejść przez dane diagnostyczne i rozwiązywaniu problemów w blogu [dane diagnostyczne obliczeniowe PaaS Azure](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Jeśli Kosz usługi w chmurze, można zresetować właściwości wdrożenia, efektywnie wymazywanie informacji na temat oryginalnego problemu.
>
>

## <a name="next-steps"></a>Następne kroki
Wyświetl więcej [Rozwiązywanie problemów z artykułów](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) dla usług w chmurze.

Aby dowiedzieć się, jak rozwiązywać problemy roli usługi w chmurze przy użyciu danych diagnostycznych na komputerze Azure PaaS, zobacz [serii blogu Kevina Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
