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
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2020
ms.locfileid: "78208227"
---
Aby wyświetlić i skopiować klucze dostępu do konta magazynu lub parametry połączenia z Azure Portal:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Odszukaj konto magazynu.
3. W obszarze **Ustawienia** wybierz pozycję **Klucze dostępu**. Zostaną wyświetlone klucze dostępu do Twojego konta, a także pełne parametry połączenia dla każdego klucza.
4. Znajdź wartość **Klucz** w obszarze **key1** i kliknij przycisk **Kopiuj**, aby skopiować klucz konta.
5. Alternatywnie można skopiować wszystkie parametry połączenia. Znajdź wartość **Parametry połączenia** w obszarze **key1**i kliknij przycisk **Kopiuj**, aby skopiować parametry połączenia.

    ![Zrzut ekranu przedstawiający sposób wyświetlania kluczy dostępu w Azure Portal](media/storage-view-keys-include/portal-connection-string.png)

Aby uzyskać dostęp do usługi Azure Storage, można użyć dowolnego klucza, ale ogólnie rzecz biorąc, dobrym sposobem jest użycie pierwszego klucza i zarezerwowanie użycia drugiego klucza podczas obracania kluczy.
