---
title: Uzyskiwanie dostępu do maszyn wirtualnych platformy Azure z poziomu Eksploratora serwera | Dokumentacja firmy Microsoft
description: Pobierz omówienie sposobu wyświetlania tworzenie i zarządzanie nimi maszyn wirtualnych (VM) w Eksploratorze serwera w programie Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/31/2017
ms.author: ghogen
ms.openlocfilehash: 81a0e2923ddbb6960066f01d6365e8c9278defac
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055540"
---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Uzyskiwanie dostępu do maszyn wirtualnych platformy Azure z poziomu Eksploratora serwera

W przypadku maszyn wirtualnych hostowanych na platformie Azure można uzyskiwać do nich dostęp w Eksploratorze serwera. Należy najpierw zalogować do subskrypcji platformy Azure do wyświetlenia w usługach mobilnych. Aby zalogować, otwórz menu skrótów dla węzła platformy Azure w Eksploratorze serwera, a następnie wybierz **Połącz platformę Microsoft Azure**.

1. W programie Cloud Explorer wybierz maszynę wirtualną, a następnie wybierz klawisz F4, aby wyświetlić okno właściwości.

    W poniższej tabeli przedstawiono, jakie właściwości są dostępne, ale są one wszystkie tylko do odczytu. Aby je zmienić, należy użyć [witryny Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

   | Właściwość | Opis |
   | --- | --- |
   | Nazwa DNS |Adres URL przy użyciu adresu internetowego maszyny wirtualnej. |
   | Środowisko |Dla maszyny wirtualnej wartość tej właściwości jest zawsze produkcji. |
   | Name (Nazwa) |Nazwa maszyny wirtualnej. |
   | Rozmiar |Rozmiar maszyny wirtualnej, która odzwierciedla ilość pamięci i miejsca na dysku, który jest dostępny. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs). |
   | Stan |Wartości obejmują uruchamianie, uruchomiono, zatrzymywania, zatrzymane i pobierania stanu. Jeśli pojawi się stan pobierania, bieżący stan jest nieznany. Wartości dla tej właściwości różnią się od wartości, które są używane na [witryny Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). |
   | Identyfikator subskrypcji |Identyfikator subskrypcji dla Twojego konta platformy Azure. Te informacje można wyświetlić na [witryny Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) , wyświetlając właściwości dla subskrypcji. |
2. Wybierz węzeł punktu końcowego, a następnie wyświetlić **właściwości** okna.
3. W poniższej tabeli opisano dostępne właściwości punktów końcowych, ale są one tylko do odczytu. Aby dodać lub edytować punkty końcowe dla maszyny wirtualnej, należy użyć [witryny Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). 

   | Właściwość | Opis |
   | --- | --- |
   | Name (Nazwa) |Identyfikator punktu końcowego. |
   | Port prywatny |Port dostępu do sieci wewnętrznej do aplikacji. |
   | Protokół |Protokół, który korzysta z warstwy transportowej dla tego punktu końcowego, TCP lub UDP. |
   | Port publiczny |Port używany dla publicznego dostępu do aplikacji. |
