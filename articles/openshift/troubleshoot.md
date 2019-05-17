---
title: Rozwiązywanie problemów z platformy Azure, Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów i rozwiązywania typowych problemów z platformy Azure Red Hat OpenShift
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 9ef32273c007e7319b7f6df99f3d904f805c31f2
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550907"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Rozwiązywanie problemów dla platformy Azure, Red Hat OpenShift

Ten artykuł szczegółowo opisuje niektóre typowe problemy podczas tworzenia lub zarządzania klastrami Microsoft Azure Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Ponawianie próby utworzenia klastra nie powiodło się

Jeśli tworzenie Azure Red Hat OpenShift klaster przy użyciu `az` interfejsu wiersza polecenia nie powiedzie się, ponawianie próby tworzenia będą w dalszym się nie powieść.
Użyj `az openshift delete` można usunąć klastra nie powiodło się, a następnie utwórz całkowicie nowego klastra.

## <a name="untrusted-azure-red-hat-openshift-server-certificate"></a>Niezaufany certyfikat serwera usługi Azure Red Hat OpenShift

Konsola OpenShift używa certyfikatu z podpisem własnym. Po wyświetleniu monitu w przeglądarce, należy pominąć to ostrzeżenie i zaakceptuj "niezaufany" certyfikat.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Ukryte grupa zasobów klastra Azure Red Hat OpenShift

Obecnie `Microsoft.ContainerService/openShiftManagedClusters` zasób, który jest automatycznie tworzona przez interfejs wiersza polecenia platformy Azure (`az openshift create` polecenie) jest ukryty w witrynie Azure portal. W **grupy zasobów** widoku wyboru **Pokaż ukryte typy** Aby wyświetlić grupę zasobów.

![Zrzut ekranu przedstawiający pole wyboru typu ukryte w portalu](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Tworzenie klastra wyniki w wyniku błędu, który nie znaleziono zarejestrowanego dostawcy zasobów

W przypadku tworzenia klastra wyników błąd `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, były częścią korzystania z wersji zapoznawczej, a teraz trzeba [zakup maszyny wirtualnej platformy Azure, zarezerwowane wystąpienia](https://aka.ms/openshift/buy) do korzystania z produktu jest ogólnie dostępna. Rezerwacja zmniejsza swoje wydatki wg wstępnie płacenia za w pełni zarządzanych usług platformy Azure. Zapoznaj się [ *co to jest Azure rezerwacje* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) Aby dowiedzieć się więcej na temat rezerwacji i jak mogą zaoszczędzić pieniądze.

## <a name="next-steps"></a>Kolejne kroki

- Spróbuj [Centrum pomocy systemu Red Hat OpenShift](https://help.openshift.com/) potrzeby więcej w OpenShift rozwiązywania problemów.

- Znajdź odpowiedzi na [często zadawane pytania dotyczące usługi Azure Red Hat OpenShift](openshift-faq.md).