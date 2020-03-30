---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391737"
---
Nie: usunięcie punktu końcowego serwera nie jest jak ponowne uruchomienie serwera! Usuwanie i ponowne tworzenie punktu końcowego serwera prawie nigdy nie jest odpowiednim rozwiązaniem do rozwiązywania problemów z synchronizacją, warstwą w chmurze lub innymi aspektami synchronizacji plików platformy Azure. Usunięcie punktu końcowego serwera jest operacją destrukcyjną. Może to spowodować utratę danych w przypadku, gdy pliki warstwowe istnieją poza obszarem nazw punktu końcowego serwera. Zobacz [Dlaczego pliki warstwowe istnieją poza obszarem nazw punktu końcowego serwera,](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) aby uzyskać więcej informacji. Lub może to spowodować niedostępne pliki dla plików warstwowych, które istnieją w obszarze nazw punktu końcowego serwera. Te problemy nie rozwiążą się po odtworzeniu punktu końcowego serwera. Pliki warstwowe mogą istnieć w obszarze nazw punktu końcowego serwera, nawet jeśli nigdy nie włączono warstw w chmurze. Dlatego zaleca się, aby nie usuwać punktu końcowego serwera, chyba że chcesz przestać używać synchronizacji plików platformy Azure z tym konkretnym folderem lub zostały wyraźnie poinstruowane przez inżyniera firmy Microsoft. Aby uzyskać więcej informacji na temat usuwania punktów końcowych serwera, zobacz [Usuwanie punktu końcowego serwera](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
