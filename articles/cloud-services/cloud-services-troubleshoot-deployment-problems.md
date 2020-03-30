---
title: Rozwiązywanie problemów z wdrażaniem usług w chmurze | Dokumenty firmy Microsoft
description: Istnieje kilka typowych problemów, które mogą wystąpić podczas wdrażania usługi w chmurze na platformie Azure. Ten artykuł zawiera rozwiązania dla niektórych z nich.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: ccb08f853ae0f941dd5f9c0eca8c77f0f650905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71122750"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Rozwiązywanie problemów z wdrażaniem usług w chmurze
Podczas wdrażania pakietu aplikacji usługi w chmurze na platformie Azure można uzyskać informacje o wdrożeniu z okienka **Właściwości** w witrynie Azure portal. Można użyć szczegółów w tym okienku, aby ułatwić rozwiązywanie problemów z usługą w chmurze i można podać te informacje do pomocy technicznej platformy Azure podczas otwierania nowego żądania pomocy technicznej.

Okienko **Właściwości** można znaleźć w następujący sposób:

* W witrynie Azure portal kliknij wdrożenie usługi w chmurze, kliknij pozycję **Wszystkie ustawienia**, a następnie kliknij pozycję **Właściwości**.

> [!NOTE]
> Zawartość okienka Właściwości można skopiować do **schowka,** klikając ikonę w prawym górnym rogu okienka.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problem: Nie mogę uzyskać dostępu do mojej witryny sieci Web, ale moje wdrożenie jest uruchomione i wszystkie wystąpienia roli są gotowe
Link adresu URL witryny sieci Web wyświetlany w portalu nie zawiera portu. Domyślny port dla witryn sieci Web to 80. Jeśli aplikacja jest skonfigurowana do uruchamiania w innym porcie, należy dodać poprawny numer portu do adresu URL podczas uzyskiwania dostępu do witryny sieci Web.

1. W witrynie Azure portal kliknij wdrożenie usługi w chmurze.
2. W okienku **Właściwości witryny** Azure portal sprawdź porty dla wystąpień roli (w obszarze **Wejściowe punkty końcowe).**
3. Jeśli port nie ma wartości 80, dodaj poprawną wartość portu do adresu URL podczas uzyskiwania dostępu do aplikacji. Aby określić port nieniewiążący, wpisz adres URL, po którym następuje dwukropek (:), a następnie numer portu, bez spacji.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problem: Moje wystąpienia roli poddane recyklingowi bez robienia czegokolwiek
Trwanie usługi odbywa się automatycznie, gdy platforma Azure wykrywa węzły problemów i w związku z tym przenosi wystąpienia roli do nowych węzłów. W takim przypadku może zostać wyświetlony sposób automatycznego odtwo niezłania recyklingu wystąpień roli. Aby dowiedzieć się, czy doszło do uzdrowienia usługi:

1. W witrynie Azure portal kliknij wdrożenie usługi w chmurze.
2. W okienku **Właściwości witryny** Azure portal przejrzyj informacje i określ, czy leczenie usługi wystąpiło w czasie, w którym obserwowano odtwarzanie ról.

Role będą również odtwarzać mniej więcej raz w miesiącu podczas aktualizacji hosta systemu operacyjnego i systemu operacyjnego gościa.  
Aby uzyskać więcej informacji, zobacz wpis w blogu [Wystąpienie roli uruchamia się ponownie z powodu uaktualnień systemu operacyjnego](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problem: Nie mogę wykonać wymiany VIP i otrzymuję błąd
Zamiana adresu VIP nie jest dozwolona, jeśli trwa aktualizacja wdrożenia. Aktualizacje wdrażania mogą być automatycznie, gdy:

* Dostępny jest nowy system operacyjny dla gościa, który jest skonfigurowany do automatycznych aktualizacji.
* Następuje uzdrowienie usługi.

Aby dowiedzieć się, czy automatyczna aktualizacja uniemożliwia ci wykonanie wymiany VIP:

1. W witrynie Azure portal kliknij wdrożenie usługi w chmurze.
2. W okienku **Właściwości witryny** Azure portal przyjrzyj się wartości **statusu**. Jeśli jest **gotowy,** a następnie sprawdź **ostatnia operacja,** aby sprawdzić, czy niedawno się stało, które mogą uniemożliwić zamianę VIP.
3. Powtórz kroki 1 i 2 dla wdrożenia produkcyjnego.
4. Jeśli aktualizacja automatyczna jest w toku, poczekaj na jej zakończenie przed podjęciem próby wymiany VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problem: Wystąpienie roli jest zapętlanie między started, inicjowanie, zajęty i zatrzymany
Ten stan może wskazywać na problem z kodem, pakietem lub plikiem konfiguracyjnym aplikacji. W takim przypadku powinieneś być w stanie zobaczyć, że stan zmienia się co kilka minut, a portal Azure może powiedzieć coś takiego jak **Recykling,** **Zajęty**lub **Inicjowanie.** Oznacza to, że coś jest nie tak z aplikacją, która utrzymuje wystąpienie roli z systemem.

Aby uzyskać więcej informacji na temat rozwiązywania tego problemu, zobacz wpis w blogu [Usługi Azure PaaS Compute Diagnostics Data](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) i typowe [problemy, które powodują, że role do recyklingu](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problem: Moja aplikacja przestała działać
1. W witrynie Azure portal kliknij wystąpienie roli.
2. W okienku **Właściwości witryny** Azure portal należy wziąć pod uwagę następujące warunki, aby rozwiązać problem:
   * Jeśli wystąpienie roli zostało niedawno zatrzymane (można sprawdzić wartość **liczby przerwań),** wdrożenie może być aktualizowane. Poczekaj, aby zobaczyć, czy wystąpienie roli wznawia działanie na własną rękę.
   * Jeśli wystąpienie roli jest **zajęty,** sprawdź kod aplikacji, aby sprawdzić, czy [zdarzenie Kontrola stanu](/previous-versions/azure/reference/ee758135(v=azure.100)) jest obsługiwane. Może być konieczne dodanie lub naprawienie kodu obsługującego to zdarzenie.
   * Przejdź przez dane diagnostyczne i scenariusze rozwiązywania problemów w blogu [Azure PaaS Compute Diagnostics Data](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Jeśli oddajesz do recyklingu usługę w chmurze, można zresetować właściwości wdrożenia, skutecznie wymazując informacje o oryginalnym problemie.
>
>

## <a name="next-steps"></a>Następne kroki
Wyświetl więcej [artykułów dotyczących rozwiązywania problemów](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) z usługami w chmurze.

Aby dowiedzieć się, jak rozwiązywać problemy z rolami usługi w chmurze przy użyciu danych diagnostycznych komputera usługi Azure PaaS, zobacz [serię blogów Kevina Williamsona.](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)
