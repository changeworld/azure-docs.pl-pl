---
title: Rozwiązywanie problemów z programem Azure Red Hat OpenShift
description: Rozwiązywanie typowych problemów z usługą Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274930"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Rozwiązywanie problemów z programem Azure Red Hat OpenShift

W tym artykule opisano niektóre typowe problemy napotkane podczas tworzenia klastrów OpenShift red hat platformy Microsoft Azure lub zarządzania nimi.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Ponowienie próby utworzenia klastra, który nie powiódł się

Jeśli utworzenie klastra OpenShift usługi `az` Azure Red Hat przy użyciu polecenia CLI nie powiedzie się, ponowienie próby utworzenia będzie nadal kończy się niepowodzeniem.
Służy `az openshift delete` do usuwania klastra, który nie powiódł się, a następnie utwórz całkowicie nowy klaster.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Ukryta grupa zasobów klastra OpenShift usługi Azure Red Hat

Obecnie `Microsoft.ContainerService/openShiftManagedClusters` zasób, który jest automatycznie tworzony przez`az openshift create` narzędzie interfejsu wiersza polecenia platformy Azure (polecenie) jest ukryty w witrynie Azure portal. W widoku **Grupa zasobów** zaznacz pole wyboru Pokaż **ukryte typy,** aby wyświetlić grupę zasobów.

![Zrzut ekranu przedstawiający pole wyboru typu ukrytego w portalu](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Utworzenie klastra powoduje błąd, który nie został znaleziony przez zarejestrowanego dostawcę zasobów

Jeśli utworzenie klastra powoduje błąd, który `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, a następnie były częścią wersji zapoznawczej i teraz trzeba kupić platformy Azure maszyny [wirtualnej wystąpienia zarezerwowane](https://aka.ms/openshift/buy) do korzystania z ogólnie dostępnego produktu. Rezerwacja zmniejsza wydatki, płacąc z góry za w pełni zarządzane usługi platformy Azure. Zapoznaj się [*z temat rezerwacji platformy Azure,*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) aby dowiedzieć się więcej o rezerwacjach i sposobie oszczędzania pieniędzy.

## <a name="next-steps"></a>Następne kroki

- Wypróbuj [Centrum pomocy Red Hat OpenShift, aby](https://help.openshift.com/) uzyskać więcej informacji na temat rozwiązywania problemów z programem OpenShift.

- Znajdź odpowiedzi na [często zadawane pytania dotyczące usługi Azure Red Hat OpenShift](openshift-faq.md).
