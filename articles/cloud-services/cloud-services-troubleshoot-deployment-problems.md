---
title: Rozwiązywanie problemów z wdrażaniem usługi cloud | Dokumentacja firmy Microsoft
description: Istnieje kilka typowych problemów, które mogą występować podczas wdrażania usługi w chmurze na platformie Azure. Ten artykuł zawiera niektóre z nich rozwiązania.
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
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: cc2a0177525013736445db5fd1befa478dc9b9b8
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916856"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Rozwiązywanie problemów z wdrażaniem usługi chmury
Podczas wdrażania pakietu aplikacji usługi w chmurze na platformie Azure, można uzyskać informacji na temat wdrożenia z **właściwości** okienko w witrynie Azure portal. Szczegółowe informacje można użyć w tym okienku, ułatwiające rozwiązywanie problemów z usługą w chmurze, a następnie można udostępnić te informacje pomocy technicznej systemu Azure podczas otwierania nowe żądanie pomocy technicznej.

Możesz znaleźć **właściwości** okienko w następujący sposób:

* W witrynie Azure portal kliknij wdrożenie usługi w chmurze, kliknij przycisk **wszystkie ustawienia**, a następnie kliknij przycisk **właściwości**.

> [!NOTE]
> Możesz skopiować zawartość **właściwości** okienko do Schowka, klikając ikonę w prawym górnym rogu okienka.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problem: Moja witryna sieci Web nie może uzyskać dostępu, ale Moje wdrożenie zostanie uruchomione i gotowe wszystkich wystąpień roli
Link adresu URL witryny sieci Web, wyświetlana w portalu nie ma portu. Domyślny port dla witryn sieci Web to 80. Jeśli aplikacja jest skonfigurowana do uruchamiania w innego portu, należy dodać poprawny numer portu do adresu URL podczas uzyskiwania dostępu do witryny sieci Web.

1. W witrynie Azure portal kliknij wdrożenie usługi w chmurze.
2. W **właściwości** okienko w witrynie Azure Portal, sprawdź porty dla wystąpień roli (w obszarze **danych wejściowych punktów końcowych**).
3. Jeśli port nie jest 80, należy dodać wartość poprawnego portu do adresu URL, gdy uzyskujesz dostęp do aplikacji. Aby określić portów innych niż domyślne, wpisz adres URL z dwukropkiem (:) i numer portu, bez spacji.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problem: Moje wystąpienia roli odtworzyć bez mnie niczym zajęty
Usługa korygujący odbywa się automatycznie, gdy Azure wykryje problem węzły i w związku z tym przenosi wystąpień roli do nowych węzłów. W takiej sytuacji można napotkać odtwarzanie automatycznie wystąpień roli. Aby dowiedzieć się, czy wystąpił naprawianiem usług:

1. W witrynie Azure portal kliknij wdrożenie usługi w chmurze.
2. W **właściwości** okienko w witrynie Azure Portal, zapoznaj się z informacjami i określenia, czy naprawianiem usług wystąpiły w czasie, który obserwuje powtarzania cykli ról.

Role będzie również Odtwórz około raz na miesiąc, podczas systemu operacyjnego hosta i aktualizacje systemu operacyjnego gościa.  
Aby uzyskać więcej informacji, zobacz wpis w blogu [roli wystąpienie powoduje ponowne uruchomienie z powodu uaktualnienia systemu operacyjnego](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problem: Nie mogę wykonać wymiany wirtualnych adresów IP i komunikat o błędzie
Wymienianie adresów VIP jest niedozwolone, jeśli aktualizacja wdrożenia jest w toku. Wdrożenia aktualizacji mogą być wykonywane automatycznie po:

* Nowy system operacyjny gościa jest dostępny, i są skonfigurowane dla aktualizacji automatycznych.
* Naprawianie usługi występuje.

Aby dowiedzieć się, jeśli aktualizacje automatyczne uniemożliwia sposób wymiany wirtualnych adresów IP:

1. W witrynie Azure portal kliknij wdrożenie usługi w chmurze.
2. W **właściwości** okienko w witrynie Azure Portal, Przyjrzyj się wartość **stan**. Jeśli jest **gotowe**, następnie sprawdź **Ostatnia operacja** aby zobaczyć, jeśli jeden niedawno się stało, może uniemożliwić wymiany wirtualnych adresów IP.
3. Powtórz kroki 1 i 2 dla wdrażania w środowisku produkcyjnym.
4. Jeśli aktualizacje automatyczne jest w toku, poczekaj na zakończenie przed próbujące przeprowadzić wymiany wirtualnych adresów IP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problem: Wystąpienie roli jest w pętli między Stanami uruchomiona, inicjowanie, zajęta i zatrzymana
Ten stan może wskazywać na problem z kodem, pakietem lub plikiem konfiguracyjnym aplikacji. W takim przypadku powinno być możliwe wyświetlić stan, zmieniając co kilka minut i witryny Azure portal może informować, podobny do **odtwarzanie**, **zajęty**, lub **inicjowanie**. Oznacza to, że jest coś jest nie tak z aplikacją, która utrzymywanie uruchamianie wystąpienia roli.

Aby uzyskać więcej informacji na temat rozwiązywania tego problemu, zobacz wpis w blogu [PaaS platformy Azure Compute dane diagnostyczne](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) i [typowe problemy, które powodują odtwarzanie ról](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problem: Moja aplikacja przestała działać
1. W witrynie Azure portal kliknij wystąpienie roli.
2. W **właściwości** okienku portalu Azure należy wziąć pod uwagę następujące warunki w celu rozwiązania problemu:
   * Jeśli wystąpienie roli ostatnio została zatrzymana (można sprawdzić wartość **liczba przerwań**), można zaktualizować wdrożenia. Poczekaj, aby zobaczyć, jeśli wystąpienie roli wznawia działanie samodzielnie.
   * Jeśli wystąpienie roli jest **zajęty**, sprawdzać Twój kod aplikacji, aby sprawdzić, czy [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) zdarzenie jest obsługiwane. Może być konieczne, dodając lub poprawiając kodu, który obsługuje to zdarzenie.
   * Przejdź przez dane diagnostyczne i rozwiązywanie problemów ze scenariuszami wpis w blogu [PaaS platformy Azure Compute dane diagnostyczne](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Jeśli możesz odtworzyć usługi w chmurze, możesz zresetować właściwości wdrożenia, efektywnie wymazywanie informacji dotyczącej problemu, oryginalnym.
>
>

## <a name="next-steps"></a>Kolejne kroki
Wyświetl więcej [artykuły dotyczące rozwiązywania problemów](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) dla usług w chmurze.

Aby dowiedzieć się, jak rozwiązywać problemy dotyczące ról usługi chmury przy użyciu danych diagnostycznych na komputerze modelu PaaS platformy Azure, zobacz [serię wpisów w blogu Kevina Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
