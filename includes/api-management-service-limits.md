---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 01/10/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 2d4c8c2c831bd6ef16f60c34a6353f4a742798f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76159019"
---
| Zasób | Limit |
| ---------------------------------------------------------------------- | -------------------------- |
| Maksymalna liczba jednostek skali | 10 na region<sup>1</sup> |
| Rozmiar pamięci podręcznej | 5 GiB na jednostkę<sup>2</sup> |
| Równoczesne połączenia zaplecza<sup>3</sup> na urząd HTTP | 2 048 na jednostkę<sup>4</sup> |
| Maksymalny rozmiar buforowanej odpowiedzi | 2 MiB |
| Maksymalny rozmiar dokumentu zasad | 256 Kib<sup>5</sup> |
| Maksymalna liczba domen bramy niestandardowej na wystąpienie usługi<sup>6</sup> | 20 |
| Maksymalna liczba certyfikatów urzędu certyfikacji na wystąpienie usługi | 10 |
| Maksymalna liczba wystąpień usługi na subskrypcję<sup>7</sup> | 20 |
| Maksymalna liczba subskrypcji na wystąpienie usługi<sup>7</sup> | 500 |
| Maksymalna liczba certyfikatów klienta na wystąpienie usługi<sup>7</sup> | 50 |
| Maksymalna liczba interfejsów API na wystąpienie usługi<sup>7</sup> | 50 |
| Maksymalna liczba operacji interfejsu API na wystąpienie usługi<sup>7</sup> | 1000 |
| Maksymalny całkowity czas trwania żądania<sup>7</sup> | 30 sekund |
| Maksymalny buforowany rozmiar ładunku<sup>7</sup> | 2 MiB |
| Maksymalny rozmiar adresu URL żądania<sup>8</sup> | 4096 bajtów |

<sup>1.</sup> Limity skalowania zależą od warstwy cenowej. Aby wyświetlić warstwy cenowe i ich limity skalowania, zobacz [Cennik zarządzania interfejsami API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2.</sup> Rozmiar pamięci podręcznej jednostki zależy od warstwy cenowej. Aby wyświetlić warstwy cenowe i ich limity skalowania, zobacz [Cennik zarządzania interfejsami API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> Połączenia są łączone i ponownie, chyba że jawnie zamknięte przez zapleczu.<br/>
<sup>4</sup> Ten limit jest na jednostkę warstw Basic, Standard i Premium. Warstwa Deweloper jest ograniczona do 1024. Ten limit nie ma zastosowania do warstwy Zużycie.<br/>
<sup>5</sup> Ten limit dotyczy warstw Basic, Standard i Premium. W warstwie Zużycie rozmiar dokumentu zasad jest ograniczony do 4 KiB.<br/>
<sup>6</sup> Ten zasób jest dostępny tylko w warstwie Premium.<br/>
<sup>7</sup> Ten zasób ma zastosowanie tylko do warstwy Zużycie.<br/>
<sup>8</sup> Dotyczy tylko warstwy Zużycie. Zawiera ciąg zapytania o długości do 2048 bajtów.<br/>
