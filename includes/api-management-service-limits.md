---
ms.openlocfilehash: ee44cd4ee5ec6fedfec46cbbb68b0c30118a4b9f
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904479"
---
---

title: Opis pliku dołączenia: Dołącz usługi plików: interfejs API do zarządzania: vladvino

MS. AssetID: 1b813833-39c8-46be-8666-fd0960cfbf04 MS. Service: API-Management MS. temat: include MS. Date: 01/10/2020 MS. Author: vlvinogr MS. Custom: include File
---| Zasób | Limit |
| ---------------------------------------------------------------------- | -------------------------- |
| Maksymalna liczba jednostek skalowania | 10 na region<sup>1</sup> |
| Rozmiar pamięci podręcznej | 5 GiB na jednostkę<sup>2</sup> |
| Współbieżne połączenia zaplecza<sup>3</sup> na urząd http | 2 048 na jednostkę<sup>4</sup> |
| Maksymalny rozmiar buforowanej odpowiedzi | 2 MiB |
| Maksymalny rozmiar dokumentu zasad | 256 KiB<sup>5</sup> |
| Maksymalna liczba niestandardowych domen bramy na wystąpienie usługi<sup>6</sup> | 20 |
| Maksymalna liczba certyfikatów urzędu certyfikacji na wystąpienie usługi | 10 |
| Maksymalna liczba wystąpień usługi na subskrypcję<sup>7</sup> | 20 |
| Maksymalna liczba subskrypcji na wystąpienie usługi<sup>7</sup> | 500 |
| Maksymalna liczba certyfikatów klienta na wystąpienie usługi<sup>7</sup> | 50 |
| Maksymalna liczba interfejsów API na wystąpienie usługi<sup>7</sup> | 50 |
| Maksymalna liczba operacji interfejsu API na wystąpienie usługi<sup>7</sup> | 1000 |
| Maksymalny łączny czas trwania żądania<sup>7</sup> | 30 sekund |
| Maksymalny rozmiar zbuforowanego ładunku<sup>7</sup> | 2 MiB |
| Maksymalny rozmiar adresu URL żądania<sup>8</sup> | 4096 bajtów |

<sup>1</sup> Limity skalowania zależą od warstwy cenowej. Aby sprawdzić warstwy cenowe i ich limity skalowania, zobacz [cennik API Management](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> Rozmiar pamięci podręcznej na jednostkę zależy od warstwy cenowej. Aby sprawdzić warstwy cenowe i ich limity skalowania, zobacz [cennik API Management](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> Połączenia są w puli i ponownie używane, chyba że zostały jawnie zamknięte przez zaplecze.<br/>
<sup>4</sup> Ten limit jest na jednostkę warstw Podstawowa, standardowa i Premium. Warstwa dewelopera jest ograniczona do 1 024. Ten limit nie dotyczy warstwy zużycia.<br/>
<sup>5</sup> Ten limit dotyczy warstw Podstawowa, standardowa i Premium. W warstwie zużycia rozmiar dokumentu zasad jest ograniczony do 4 KiB.<br/>
<sup>6</sup> Ten zasób jest dostępny tylko w warstwie Premium.<br/>
<sup>7</sup> Ten zasób ma zastosowanie tylko do warstwy zużycia.<br/>
<sup>8</sup> Dotyczy tylko warstwy zużycia. Zawiera ciąg zapytania o długości do 2048 bajtów.<br/>
