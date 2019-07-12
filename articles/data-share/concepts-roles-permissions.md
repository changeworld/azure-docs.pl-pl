---
title: Role i wymagania dotyczące udziału danych platformy Azure w wersji zapoznawczej
description: Role i wymagania dotyczące udziału danych platformy Azure w wersji zapoznawczej
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: a5d70b9aa611b4f939cb46b5d25655edd818cb35
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807537"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Role i wymagania dotyczące udziału danych platformy Azure w wersji zapoznawczej

W tym artykule opisano role wymagane do udostępniania danych przy użyciu Azure udostępnianie Podgląd danych, również zaakceptować i odbierać dane za pomocą udostępniania danych platformy Azure w wersji zapoznawczej. 

## <a name="roles-and-requirements"></a>Role i wymagania

Aby udostępnić lub odbierać dane za pomocą udostępniania danych platformy Azure, konto użytkownika, która umożliwia logowanie do platformy Azure musi być może przyznać uprawnienia udostępniania danych do konta magazynu, że udostępnianie danych z lub odbierania danych w celu. Zazwyczaj jest to uprawnienie, który znajduje się w **właściciela** roli lub niestandardową rolę z uprawnieniami do zapisu przypisania Microsoft.Authorization/role przypisane. 

Aby udostępnić lub odbierać dane z lub do konta usługi Azure Storage, musisz być właścicielem konta magazynu. Nawet jeśli utworzono konto magazynu, to nie powoduje automatycznego przyznania możesz własności konta magazynu. Aby dodać siebie w roli właściciela konta usługi Azure Storage, wykonaj następujące kroki.

1. Przejdź do konta magazynu w witrynie Azure portal
1. Wybierz **kontrola dostępu (IAM)**
1. Kliknij przycisk **Dodaj**
1. Dodaj siebie w jako właściciela

W witrynie Azure Portal wybierz swoją nazwę użytkownika w prawym górnym rogu, a następnie wybierz pozycję **Uprawnienia**, aby wyświetlić uprawnienia, które masz w subskrypcji. Jeśli masz dostęp do wielu subskrypcji, wybierz odpowiednią subskrypcję. 

## <a name="resource-provider-registration"></a>Rejestracja dostawcy zasobów 

Trwa akceptowanie zaproszenia udziału danych platformy Azure, należy ręcznie zarejestrować dostawcę zasobów Microsoft.DataShare w Twojej subskrypcji. Wykonaj następujące kroki, aby zarejestrować dostawcę zasobów Microsoft.DataShare do Twojej subskrypcji platformy Azure. 

1. W witrynie Azure portal przejdź do **subskrypcji**
1. Wybierz subskrypcję, której używasz dla udziału danych platformy Azure
1. Kliknij pozycję **dostawców zasobów**
1. Wyszukaj Microsoft.DataShare
1. Kliknij przycisk **zarejestrować**

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o rolach na platformie Azure — [poznać definicje ról](../role-based-access-control/role-definitions.md)

