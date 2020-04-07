---
title: Materiały PBR
description: Opisuje typ materiału PBR.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 64553506f75451c50a87932904f00a7275ea9286
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680260"
---
# <a name="pbr-materials"></a>Materiały PBR

*Materiały PBR* są jednym z obsługiwanych [typów materiałów](../../concepts/materials.md) w renderowaniu zdalnym platformy Azure. Są one używane do [oczek,](../../concepts/meshes.md) które powinny otrzymać realistyczne oświetlenie.

PBR oznacza **P**hysically **B**ased **R**endering i oznacza, że materiał opisuje właściwości wizualne powierzchni w sposób fizycznie wiarygodne, tak, że realistyczne wyniki są możliwe w każdych warunkach oświetleniowych. Większość nowoczesnych silników gier i narzędzi do tworzenia treści obsługuje materiały PBR, ponieważ są one uważane za najlepsze przybliżenie rzeczywistych scenariuszy renderowania w czasie rzeczywistym.

![Model próbki glTF kasku renderowany przez ARR](media/helmet.png)

Materiały PBR nie są jednak rozwiązaniem uniwersalnym. Istnieją materiały, które odbijają kolor inaczej w zależności od kąta widzenia. Na przykład, niektóre tkaniny lub farby samochodowe. Tego rodzaju materiały nie są obsługiwane przez standardowy model PBR i obecnie nie są obsługiwane przez zdalne renderowanie platformy Azure. Obejmuje to rozszerzenia PBR, takie jak *Cienka folia* (powierzchnie wielowarstwowe) i *Clear-Coat* (do farb samochodowych).

## <a name="common-material-properties"></a>Wspólne właściwości materiału

Właściwości te są wspólne dla wszystkich materiałów:

* **albedoColor:** Ten kolor jest mnożony z innymi kolorami, takimi jak *albedoMap* lub *kolory wierzchołków*. Jeśli *przezroczystość* jest włączona na materiale, kanał alfa jest `1` używany do regulacji `0` krycia, co oznacza, że jest w pełni nieprzezroczysty i oznacza w pełni przezroczysty. Wartość domyślna to biały.

  > [!NOTE]
  > Gdy materiał PBR jest w pełni przezroczysty, jak idealnie czysty kawałek szkła, nadal odzwierciedla środowisko. Jasne plamy, takie jak słońce, są nadal widoczne w refleksji. Inaczej jest w przypadku [materiałów kolorowych](color-materials.md).

* **albedoMap:** [Tekstura 2D](../../concepts/textures.md) dla wartości albedo na piksel.

* **alphaClipEnabled** i **alphaClipThreshold:** Jeśli *alphaClipEnabled* jest prawdziwe, wszystkie piksele, w których wartość alfa albedo jest niższa niż *alfaClipThreshold* nie zostaną narysowane. Przycinanie alfa może być używane nawet bez włączania przezroczystości i jest znacznie szybsze do renderowania. Alpha obcięte materiały są nadal wolniejsze do renderowania niż w pełni nieprzezroczyste materiały, choć. Domyślnie przycinanie alfa jest wyłączone.

* **textureCoordinateScale** i **textureCoordinateOffset:** Skala jest mnożona do współrzędnych tekstury UV, do niej dodaje się przesunięcie. Może być używany do rozciągania i przesuwania tekstur. Domyślna skala to (1, 1), a przesunięcie to (0, 0).

* **useVertexColor:** Jeśli siatka zawiera kolory wierzchołków i ta opcja jest włączona, kolory wierzchołków siatki są mnożone do *albedoColor* i *albedoMap*. Domyślnie kolory wierzchołków są wyłączone.

* **isDoubleSided:** Jeśli dwustronność jest ustawiona na true, trójkąty z tym materiałem są renderowane, nawet jeśli aparat patrzy na ich tylne ściany. W przypadku materiałów PBR oświetlenie jest również prawidłowo obliczane dla tylnych ścian. Domyślnie ta opcja jest wyłączona. Zobacz też [Renderowanie jednostronne](single-sided-rendering.md).

## <a name="pbr-material-properties"></a>Właściwości materiału PBR

Podstawową ideą renderowania opartego fizycznie jest użycie właściwości *BaseColor,* *Metalness*i *Roughness* do emulowania szerokiej gamy rzeczywistych materiałów. Szczegółowy opis PBR wykracza poza zakres tego artykułu. Aby uzyskać więcej informacji na temat PBR, zobacz [inne źródła](http://www.pbr-book.org). Następujące właściwości są specyficzne dla materiałów PBR:

* **baseKolor:** W materiałach PBR *kolor albedo* jest określany jako *kolor bazowy.* W usłudze Azure Remote Rendering właściwość *koloru albedo* jest już obecna za pośrednictwem wspólnych właściwości materiału, więc nie ma żadnej dodatkowej właściwości koloru bazowego.

* **chropowatość** i **chropowatośćMap:** Chropowatość określa, jak chropowatość jest chropowata lub gładka. Szorstkie powierzchnie rozpraszają światło w większej liczbie kierunków niż gładkie powierzchnie, co sprawia, że odbicia są niewyraźne, a nie ostre. Zakres wartości wynosi `0.0` `1.0`od do . Gdy `roughness` jest `0.0`równa , odbicia będą ostre. Gdy `roughness` jest `0.5`równa , odbicia staną się rozmazane.

  Jeśli podano zarówno wartość chropowatości, jak i mapę chropowatości, wartość końcowa będzie wynikiem tych dwóch.

* **metalowość** i **metalowośćMapa:** W fizyce ta właściwość odpowiada, czy powierzchnia jest przewodząca, czy dielektryczna. Materiały przewodzące mają różne właściwości odblaskowe i wydają się być odblaskowe bez koloru albedo. W materiałach PBR ta właściwość wpływa na to, jak bardzo powierzchnia odzwierciedla otaczające środowisko. Wartości wahają `0.0` `1.0`się od do . Gdy metalowość jest, `0.0`kolor albedo jest w pełni widoczny, a materiał wygląda jak plastik lub ceramika. Gdy metalowość jest `0.5`, wygląda jak malowany metal. Gdy metalowość jest `1.0`, powierzchnia prawie całkowicie traci swój kolor albedo i odzwierciedla tylko otoczenie. Na przykład, `metalness` `1.0` jeśli `roughness` `0.0` jest i jest to powierzchnia wygląda jak prawdziwe lustro.

  Jeśli podano zarówno wartość metalowości, jak i mapę metalowości, wartość końcowa będzie wynikiem tych dwóch.

  ![metalność i chropowatość](./media/metalness-roughness.png)

  Na powyższym zdjęciu kula w prawym dolnym rogu wygląda jak prawdziwy metalowy materiał, lewy dolny róg wygląda jak ceramika lub plastik. Kolor albedo zmienia się również w zależności od właściwości fizycznych. Wraz ze wzrostem chropowatości materiał traci ostrość odbicia.

* **normalMap:** Aby symulować szczegółowe dane, można zapewnić [normalną mapę.](https://en.wikipedia.org/wiki/Normal_mapping)

* **okluzjiMapa** i **aoScale:** [Okluzja otoczenia](https://en.wikipedia.org/wiki/Ambient_occlusion) sprawia, że obiekty z szczelinami wyglądają bardziej realistycznie, dodając cienie do obszarów okluzjonych. Wartość okluzji `0.0` waha `1.0`się `0.0` od do , gdzie `1.0` oznacza ciemność (okludy) i oznacza brak okluzji. Jeśli tekstura 2D jest dostarczana jako mapa okluzji, efekt jest włączony i *aoScale* działa jako mnożnik.

  ![Mapa okluzji](./media/boom-box-ao2.gif)

* **przejrzyste:** W przypadku materiałów PBR istnieje tylko jedno ustawienie przezroczystości: jest włączone lub nie. Krycie jest definiowane przez kanał alfa koloru albedo. Po włączeniu potok renderowania bardziej złożony jest wywoływany do rysowania powierzchni półprzezroczystych. Renderowanie zdalne platformy Azure implementuje [przezroczystość niezależną od](https://en.wikipedia.org/wiki/Order-independent_transparency) rzeczywistych zamówień (OIT).

  Przezroczysta geometria jest kosztowna do renderowania. Jeśli potrzebujesz tylko otworów w powierzchni, na przykład dla liści drzewa, lepiej jest użyć przycinania alfa.

  ![Przejrzystość](./media/transparency.png) Zawiadomienie na powyższym obrazie, jak prawo najbardziej kula jest w pełni przezroczyste, ale odbicie jest nadal widoczne.

  > [!IMPORTANT]
  > Jeśli dowolny materiał ma zostać przełączony z nieprzezroczystego na przezroczysty w czasie wykonywania, moduł renderowania musi użyć [trybu renderowania](../../concepts/rendering-modes.md) *TileBasedComposition* . Ograniczenie to nie ma zastosowania do materiałów, które są konwertowane jako przezroczyste materiały na początek.

## <a name="technical-details"></a>Szczegóły techniczne

Renderowanie zdalne platformy Azure używa programu BrDF mikroasetowego Cook-Torrance z GGX NDF, Schlick Fresnel i terminem GGX Smith skorelowanym terminem widoczności z terminem rozproszonym Lamberta. Ten model jest obecnie de facto standardem branżowym. Aby uzyskać więcej szczegółowych informacji, zapoznaj się z tym [artykułem: Renderowanie oparte fizycznie - Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Alternatywą dla modelu PBR *metalness-Roughness* używanego w renderowaniu zdalnym platformy Azure jest model PBR *specular-Glossiness.* Ten model może reprezentować szerszą gamę materiałów. Jednak jest droższy i zwykle nie działa dobrze w przypadkach w czasie rzeczywistym.
Nie zawsze jest możliwe przekonwertowanie z *Specular-Glossiness* na *Metalness-Roughness,* ponieważ istnieją pary wartości *(rozproszone, specularne),* które nie mogą być konwertowane na *(BaseColor, Metalness)*. Konwersja w drugim kierunku jest prostsza i bardziej precyzyjna, ponieważ wszystkie pary *(BaseColor, Metalness)* odpowiadają dobrze zdefiniowanym *parom (rozproszonym, specularnym).*

## <a name="next-steps"></a>Następne kroki

* [Materiały kolorowe](color-materials.md)
* [Tekstury](../../concepts/textures.md)
* [Siatki](../../concepts/meshes.md)
