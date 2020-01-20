---
title: Rozwiązywanie problemów z usługą Azure Red Hat OpenShift
description: Rozwiązywanie typowych problemów z usługą Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274930"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Rozwiązywanie problemów z usługą Azure Red Hat OpenShift

W tym artykule opisano niektóre typowe problemy występujące podczas tworzenia i zarządzania Microsoft Azure klastrów Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Ponawianie próby utworzenia klastra, który uległ awarii

Jeśli utworzenie klastra Red Hat OpenShift na platformie Azure przy użyciu polecenia interfejsu CLI `az` nie powiedzie się, ponawianie próby utworzenia będzie kończyć się niepowodzeniem.
Użyj `az openshift delete`, aby usunąć uszkodzony klaster, a następnie utwórz zupełnie nowy klaster.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Ukryta Grupa zasobów klastra usługi Azure Red Hat OpenShift

Obecnie `Microsoft.ContainerService/openShiftManagedClusters` zasobów, które są tworzone automatycznie za pomocą interfejsu wiersza polecenia platformy Azure (polecenie`az openshift create`), są ukryte w Azure Portal. W widoku **grupy zasobów** zaznacz opcję **Pokaż ukryte typy** , aby wyświetlić grupę zasobów.

![Zrzut ekranu przedstawiający pole wyboru typu ukrytego w portalu](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Tworzenie klastra powoduje błąd, który nie znalazł zarejestrowanego dostawcy zasobów

W przypadku tworzenia klastra Wystąpił błąd, który `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, a następnie był częścią wersji zapoznawczej, a teraz trzeba [zakupić zarezerwowane wystąpienia maszyn wirtualnych platformy Azure](https://aka.ms/openshift/buy) , aby korzystać z ogólnie dostępnego produktu. Rezerwacja zmniejsza wydatki dzięki przedpłaceniu za w pełni zarządzane usługi platformy Azure. Zapoznaj się z tematami [*Azure Reservations*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) , aby dowiedzieć się więcej o rezerwacjach i sposobach ich oszczędności.

## <a name="next-steps"></a>Następne kroki

- Wypróbuj [centrum pomocy Red Hat OpenShift](https://help.openshift.com/) , aby uzyskać więcej informacji na temat rozwiązywania problemów z OpenShift.

- Znajdź odpowiedzi na [często zadawane pytania dotyczące usługi Azure Red Hat OpenShift](openshift-faq.md).
