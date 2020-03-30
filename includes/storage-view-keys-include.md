---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9333bb36971fc28a23a443e50d191abeef05b758
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208227"
---
Aby wyświetlić i skopiować klucze dostępu do konta magazynu lub parametry połączenia z witryny Azure Portal:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
2. Odszukaj konto magazynu.
3. W obszarze **Ustawienia**wybierz pozycję **Klawisze dostępu**. Zostaną wyświetlone klucze dostępu do Twojego konta, a także pełne parametry połączenia dla każdego klucza.
4. Znajdź wartość **Klucz** w obszarze **key1** i kliknij przycisk **Kopiuj**, aby skopiować klucz konta.
5. Alternatywnie można skopiować cały ciąg połączenia. Znajdź wartość **Parametry połączenia** w obszarze **key1**i kliknij przycisk **Kopiuj**, aby skopiować parametry połączenia.

    ![Zrzut ekranu przedstawiający sposób wyświetlania kluczy dostępu w witrynie Azure portal](media/storage-view-keys-include/portal-connection-string.png)

Możesz użyć albo klucz, aby uzyskać dostęp do usługi Azure Storage, ale ogólnie jest dobrą praktyką, aby użyć pierwszego klucza i zarezerwować użycie drugiego klucza podczas obracania kluczy.
