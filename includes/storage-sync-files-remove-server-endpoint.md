---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33dff710d83bd12a8db343a89c6e4576d1397ba7
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738687"
---
Brak: usuwanie punktu końcowego serwera nie jest takie jak ponowne uruchomienie serwera! Usunięcie i ponowne utworzenie serwera punktu końcowego jest prawie nigdy odpowiednie rozwiązanie do rozwiązywania problemów z synchronizacji, chmury, dodając funkcje warstw ani innych aspektów synchronizacji plików Azure. Usuwanie punktu końcowego serwera jest operacją destrukcyjnego i może spowodować utratę danych w przypadku gdy pliki do warstwy istnieje poza nazw punktu końcowego serwera (zobacz [Dlaczego warstwowych pliki znajdują się poza nazw punktu końcowego serwera](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) dla więcej informacji) lub niedostępne pliki warstwowych pliki istniejące w przestrzeni nazw punktu końcowego serwera. Te problemy nie rozwiąże, gdy jest tworzona ponownie punkt końcowy serwera. Warstwowych plików może istnieć w obszarze nazw punktu końcowego serwera, nawet jeśli nigdy nie była chmury, dodając funkcje warstw włączone. Dlatego zaleca się, że nie spowoduje to usunięcia serwera punktu końcowego, chyba że chcesz zaprzestać korzystania z tego konkretnego folderu synchronizacji plików Azure lub jawnie zalecił Aby to zrobić przez specjalistę firmy Microsoft. Aby uzyskać więcej informacji, Usuń serwer z punktów końcowych, zobacz [usunąć punktu końcowego serwera](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    