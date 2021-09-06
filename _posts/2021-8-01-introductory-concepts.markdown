# 图形学概念导论

> 光线追踪器「raytracer」和光栅化器「rasterizer」采用不同的方法将3d场景渲染到2d屏幕上。然而，这两种方法都有一些共同的基本概念。这篇文章将在canvas上的像素「pixels」来定义坐标系「coordinate system」,以及如何表示和操作颜色.

## The Canvas

在接下来几个章节里，我们将在Canvas上绘制东西: 一个矩形的像素数组，可以单独着色。我们将是在一个2D的屏幕上表示一个3D场景,因此我们首先要知道渲染这个抽象的矩形像素数组。

我们将用一个非常简单点的function来完成这个任务💡,为画布上的一个像素分配一种颜色:

```
canvas.PutPixel(x, y, color)
```


这个方法有三个参数: x 坐标、 y 坐标和颜色。现在让我们把注意力集中在坐标「coordinates」上。

### 坐标系统「Coordinate Systems」


canvas有一个width和一个height(以pixels为单位) ，我们称之为 Cw 和 Ch。我们需要一个坐标系来指代它的像素。对于大多数计算机屏幕，原点位于左上角; x 向屏幕右边增加，y 向底部增加，如图1-1所示。
![Figure 1-1: The coordinate system used by most computer screens](https://gabrielgambetta.com/computer-graphics-from-scratch/images/00-coordinates1.png)

<center>图1-1: 大多数计算机屏幕使用的坐标系</center>


对计算机来说，这种坐标系是自然,但对于人类并不是。3d图形程序员倾向于使用通常用于在纸上绘制图形的坐标系: 原点在中间，x向右增加，向左减少，y向上增加，向下减少，如图1-2所示。

![Figure 1-2: The coordinate system we’ll use for our canvas](https://gabrielgambetta.com/computer-graphics-from-scratch/images/00-coordinates2.png)
<center>图1-2: 我们在Canvas上使用的坐标系</center>

使用这个坐标系, x坐标的范围是<span class="math inline"><span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-8-Frame" tabindex="0" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mo stretchy=&quot;false&quot;>[</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mfrac><mrow><mo>&amp;#x2212;</mo><msub><mi>C</mi><mi>w</mi></msub></mrow><mn>2</mn></mfrac></mrow><mo>,</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mfrac><msub><mi>C</mi><mi>w</mi></msub><mn>2</mn></mfrac></mrow><mo stretchy=&quot;false&quot;>)</mo></math>" role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-26" style="width: 4.846em; display: inline-block;"><span style="display: inline-block; position: relative; width: 4.013em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.357em, 1003.96em, 3.023em, -999.997em); top: -2.497em; left: 0em;"><span class="mrow" id="MathJax-Span-27"><span class="mo" id="MathJax-Span-28" style="font-family: STIXGeneral-Regular;">[</span><span class="texatom" id="MathJax-Span-29"><span class="mrow" id="MathJax-Span-30"><span class="mfrac" id="MathJax-Span-31"><span style="display: inline-block; position: relative; width: 1.461em; height: 0px; margin-right: 0.107em; margin-left: 0.107em;"><span style="position: absolute; clip: rect(3.388em, 1001.36em, 4.273em, -999.997em); top: -4.529em; left: 50%; margin-left: -0.674em;"><span class="mrow" id="MathJax-Span-32"><span class="mo" id="MathJax-Span-33" style="font-size: 70.7%; font-family: STIXGeneral-Regular;">−</span><span class="msubsup" id="MathJax-Span-34"><span style="display: inline-block; position: relative; width: 0.836em; height: 0px;"><span style="position: absolute; clip: rect(3.388em, 1000.47em, 4.169em, -999.997em); top: -4.008em; left: 0em;"><span class="mi" id="MathJax-Span-35" style="font-size: 70.7%; font-family: STIXGeneral-Italic;">C<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; top: -3.904em; left: 0.471em;"><span class="mi" id="MathJax-Span-36" style="font-size: 50%; font-family: STIXGeneral-Italic;">w</span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; clip: rect(3.388em, 1000.32em, 4.169em, -999.997em); top: -3.643em; left: 50%; margin-left: -0.154em;"><span class="mn" id="MathJax-Span-37" style="font-size: 70.7%; font-family: STIXGeneral-Regular;">2</span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; clip: rect(0.836em, 1001.46em, 1.201em, -999.997em); top: -1.247em; left: 0em;"><span style="display: inline-block; overflow: hidden; vertical-align: 0em; border-top: 1.3px solid; width: 1.461em; height: 0px;"></span><span style="display: inline-block; width: 0px; height: 1.044em;"></span></span></span></span></span></span><span class="mo" id="MathJax-Span-38" style="font-family: STIXGeneral-Regular;">,</span><span class="texatom" id="MathJax-Span-39" style="padding-left: 0.211em;"><span class="mrow" id="MathJax-Span-40"><span class="mfrac" id="MathJax-Span-41"><span style="display: inline-block; position: relative; width: 0.992em; height: 0px; margin-right: 0.107em; margin-left: 0.107em;"><span style="position: absolute; clip: rect(3.388em, 1000.84em, 4.273em, -999.997em); top: -4.529em; left: 50%; margin-left: -0.414em;"><span class="msubsup" id="MathJax-Span-42"><span style="display: inline-block; position: relative; width: 0.836em; height: 0px;"><span style="position: absolute; clip: rect(3.388em, 1000.47em, 4.169em, -999.997em); top: -4.008em; left: 0em;"><span class="mi" id="MathJax-Span-43" style="font-size: 70.7%; font-family: STIXGeneral-Italic;">C<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; top: -3.904em; left: 0.471em;"><span class="mi" id="MathJax-Span-44" style="font-size: 50%; font-family: STIXGeneral-Italic;">w</span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span></span></span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; clip: rect(3.388em, 1000.32em, 4.169em, -999.997em); top: -3.643em; left: 50%; margin-left: -0.154em;"><span class="mn" id="MathJax-Span-45" style="font-size: 70.7%; font-family: STIXGeneral-Regular;">2</span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; clip: rect(0.836em, 1000.99em, 1.201em, -999.997em); top: -1.247em; left: 0em;"><span style="display: inline-block; overflow: hidden; vertical-align: 0em; border-top: 1.3px solid; width: 0.992em; height: 0px;"></span><span style="display: inline-block; width: 0px; height: 1.044em;"></span></span></span></span></span></span><span class="mo" id="MathJax-Span-46" style="font-family: STIXGeneral-Regular;">)</span></span><span style="display: inline-block; width: 0px; height: 2.503em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.497em; border-left: 0px solid; width: 0px; height: 1.753em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"></span></span><script type="math/tex" id="MathJax-Element-8">[{-C_w \over 2}, {C_w \over 2})</script></span> y 坐标的范围是 <span class="math inline"><span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-10-Frame" tabindex="0" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mo stretchy=&quot;false&quot;>[</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mfrac><mrow><mo>&amp;#x2212;</mo><msub><mi>C</mi><mi>h</mi></msub></mrow><mn>2</mn></mfrac></mrow><mo>,</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mfrac><msub><mi>C</mi><mi>h</mi></msub><mn>2</mn></mfrac></mrow><mo stretchy=&quot;false&quot;>)</mo></math>" role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-50" style="width: 4.586em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.805em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.357em, 1003.75em, 3.023em, -999.997em); top: -2.497em; left: 0em;"><span class="mrow" id="MathJax-Span-51"><span class="mo" id="MathJax-Span-52" style="font-family: STIXGeneral-Regular;">[</span><span class="texatom" id="MathJax-Span-53"><span class="mrow" id="MathJax-Span-54"><span class="mfrac" id="MathJax-Span-55"><span style="display: inline-block; position: relative; width: 1.357em; height: 0px; margin-right: 0.107em; margin-left: 0.107em;"><span style="position: absolute; clip: rect(3.388em, 1001.25em, 4.273em, -999.997em); top: -4.529em; left: 50%; margin-left: -0.622em;"><span class="mrow" id="MathJax-Span-56"><span class="mo" id="MathJax-Span-57" style="font-size: 70.7%; font-family: STIXGeneral-Regular;">−</span><span class="msubsup" id="MathJax-Span-58"><span style="display: inline-block; position: relative; width: 0.784em; height: 0px;"><span style="position: absolute; clip: rect(3.388em, 1000.47em, 4.169em, -999.997em); top: -4.008em; left: 0em;"><span class="mi" id="MathJax-Span-59" style="font-size: 70.7%; font-family: STIXGeneral-Italic;">C<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; top: -3.904em; left: 0.471em;"><span class="mi" id="MathJax-Span-60" style="font-size: 50%; font-family: STIXGeneral-Italic;">h</span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; clip: rect(3.388em, 1000.32em, 4.169em, -999.997em); top: -3.643em; left: 50%; margin-left: -0.154em;"><span class="mn" id="MathJax-Span-61" style="font-size: 70.7%; font-family: STIXGeneral-Regular;">2</span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; clip: rect(0.836em, 1001.36em, 1.201em, -999.997em); top: -1.247em; left: 0em;"><span style="display: inline-block; overflow: hidden; vertical-align: 0em; border-top: 1.3px solid; width: 1.357em; height: 0px;"></span><span style="display: inline-block; width: 0px; height: 1.044em;"></span></span></span></span></span></span><span class="mo" id="MathJax-Span-62" style="font-family: STIXGeneral-Regular;">,</span><span class="texatom" id="MathJax-Span-63" style="padding-left: 0.211em;"><span class="mrow" id="MathJax-Span-64"><span class="mfrac" id="MathJax-Span-65"><span style="display: inline-block; position: relative; width: 0.888em; height: 0px; margin-right: 0.107em; margin-left: 0.107em;"><span style="position: absolute; clip: rect(3.388em, 1000.78em, 4.273em, -999.997em); top: -4.529em; left: 50%; margin-left: -0.362em;"><span class="msubsup" id="MathJax-Span-66"><span style="display: inline-block; position: relative; width: 0.784em; height: 0px;"><span style="position: absolute; clip: rect(3.388em, 1000.47em, 4.169em, -999.997em); top: -4.008em; left: 0em;"><span class="mi" id="MathJax-Span-67" style="font-size: 70.7%; font-family: STIXGeneral-Italic;">C<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; top: -3.904em; left: 0.471em;"><span class="mi" id="MathJax-Span-68" style="font-size: 50%; font-family: STIXGeneral-Italic;">h</span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span></span></span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; clip: rect(3.388em, 1000.32em, 4.169em, -999.997em); top: -3.643em; left: 50%; margin-left: -0.154em;"><span class="mn" id="MathJax-Span-69" style="font-size: 70.7%; font-family: STIXGeneral-Regular;">2</span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; clip: rect(0.836em, 1000.89em, 1.201em, -999.997em); top: -1.247em; left: 0em;"><span style="display: inline-block; overflow: hidden; vertical-align: 0em; border-top: 1.3px solid; width: 0.888em; height: 0px;"></span><span style="display: inline-block; width: 0px; height: 1.044em;"></span></span></span></span></span></span><span class="mo" id="MathJax-Span-70" style="font-family: STIXGeneral-Regular;">)</span></span><span style="display: inline-block; width: 0px; height: 2.503em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.497em; border-left: 0px solid; width: 0px; height: 1.753em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"></span></span><script type="math/tex" id="MathJax-Element-10">[{-C_h \over 2}, {C_h \over 2})</script></span>. 我们只让 `PutPixel` 在这个范围内有效.


In our examples, the canvas will be drawn on the screen, so we’ll need to convert from one coordinate system to the other. To do this, we need to change the center of the coordinate system and reverse the direction of the YY axis. The resulting conversion equations are:



Sx=Cw2+CxSx=Cw2+Cx





Sy=Ch2−CySy=Ch2−Cy



We will assume `PutPixel` does this conversion automatically; from this point on, we can think of the canvas as having its coordinate origin at the center, with xx increasing to the right and yy increasing to the top of the screen.

Let’s take a look at the remaining argument of `PutPixel`: the color.

## [Color Models](https://gabrielgambetta.com/computer-graphics-from-scratch/01-common-concepts.html#color-models)

The theory of how color works is fascinating, but it’s outside the scope of this book. The following is a simplified version of the aspects relevant to us.

When light hits our eyes, it stimulates the light-sensitive cells at their back. These generate signals on our brains, that depend on the wavelength of the incoming light. We call the subjective experience of these brain signals *colors*.

We can only see light in a certain range of wavelengths, called the *visible range*. Wavelength and frequency are inversely related: the more frequently the wave hits, the smaller the distance between the peaks of that wave, and the more energy is carried by the wave. This is why infrared (wavelengths longer than 740 nm, corresponding to frequencies lower than 405 terahertz [THz]) is harmless, but ultraviolet (wavelengths shorter than 380 nm, corresponding to frequencies higher than 790 THz) can burn your skin.

Every color imaginable can be described as a combination of different wavelengths. For example, “White” is the sum of all wavelengths, while “black” is the absence of any light. It would be impractical to describe colors by listing all the wavelengths they’re made of; fortunately, it’s possible to describe almost all colors as a linear combination of just three colors, which we call *primary colors*.

### [Subtractive Color Model](https://gabrielgambetta.com/computer-graphics-from-scratch/01-common-concepts.html#subtractive-color-model)

The *subtractive color model* is a fancy name for that thing you did with crayons as a toddler. You take a white piece of paper and red, blue, and yellow crayons. You draw a yellow circle, then a blue circle that overlaps it, and you get green! Yellow and red—orange! Red and blue—purple! Mix the three together—something darkish! Wasn’t kindergarten amazing? Figure 1-3 shows the primary colors of the subtractive model, and the colors that result from mixing them.

![Figure 1-3: Subtractive primary colors and their combinations](https://gabrielgambetta.com/computer-graphics-from-scratch/images/01-primaries1.png)Figure 1-3: Subtractive primary colors and their combinations

Objects are of different colors because they absorb and reflect light in different ways. Let’s start with white light, like sunlight (sunlight isn’t quite white, but it’s close enough for our purposes). White light contains light of every wavelength. When it hits an object, the object’s surface absorbs some of the wavelengths and reflects others, depending on the material. Some of the reflected light then hits our eyes, and our brains convert that to color. What color? The sum of the wavelengths that were reflected by the surface.

So what’s going on with the crayons? You start with white light reflecting off the paper. Since it’s white paper, it reflects most of the light it receives. When you draw with a “yellow” crayon, you’re adding a layer of a material that absorbs some wavelengths but lets others pass through it. They’re reflected by the paper, pass through the yellow layer again, hit your eyes, and your brain interprets that particular combination of wavelengths as “yellow.” What the yellow layer does is *subtract* a bunch of wavelengths from the original white light.

You can think of each colored circle as a filter: when you draw a blue circle overlapping the yellow one, you’re filtering out even more wavelengths from the original light, so what hits your eyes is whatever wavelengths weren’t filtered by either the blue or the yellow circles, which your brain sees as “green.”

In summary, we start with all wavelengths and subtract some amount of the primary colors to create any other color. This color model gets its name from the fact that we’re creating colors by subtracting wavelengths.

This model isn’t quite right, though. The actual primary colors in the subtractive model aren’t the blue, red, and yellow taught to toddlers and art students, but cyan, magenta, and yellow. Furthermore, mixing the three primary colors produces a somewhat darkish color that isn’t quite black, so pure black is added as a fourth “primary.” Because B is used to represent blue, black is denoted by K, and so we arrive at the *CMYK color model* (Figure 1-4).

You can see evidence of this color model directly on the cartridges of color printers, or sometimes in the shapes of cheaply printed flyers where the different colors are slightly offset from one another.

![Figure 1-4: The four subtractive primary colors used by printers](https://gabrielgambetta.com/computer-graphics-from-scratch/images/01-primaries2.png)Figure 1-4: The four subtractive primary colors used by printers

### [Additive Color Model](https://gabrielgambetta.com/computer-graphics-from-scratch/01-common-concepts.html#additive-color-model)

The subtractive color model is only half the story. If you’ve ever looked at a screen up close or with a magnifying glass (or, let’s be honest, accidentally sneezed on it), you’ve probably seen tiny colored dots: these are red, green, and blue.

Computer screens are the opposite of paper. Paper doesn’t emit light; it merely reflects part of the light that hits it. Screens, on the other hand, are black, but they do emit light on their own. With paper, we start with white light and *subtract* the wavelengths we don’t want; with a screen, we start with no light and *add* the wavelengths we want.

Different primary colors are necessary for this. Most colors can be created by adding different amounts of red, green, and blue to a black surface; this is the *RGB color model*, an *additive color model*, shown in Figure 1-5.

The combination of additive primary colors is *lighter* than its components, whereas the combination of subtractive primary colors is *darker*; all the additive primaries add up to white, while all the subtractive primaries add up to black.

![Figure 1-5: The additive primary colors and some of their combinations](https://gabrielgambetta.com/computer-graphics-from-scratch/images/01-primaries3.png)Figure 1-5: The additive primary colors and some of their combinations

### [Forget the Details](https://gabrielgambetta.com/computer-graphics-from-scratch/01-common-concepts.html#forget-the-details)

Now that you know all this, you can selectively forget most of the details and focus on what’s important for our work.

Most colors can be represented in either RGB or CMYK (or in any of the many other color models), and it’s possible to convert from one *color space* to another. Since we’re focusing on rendering things on a screen, we use the RGB color model for the rest of this book.

As described above, objects absorb part of the light reaching them and reflect the rest. Which wavelengths are absorbed and which are reflected is what we perceive as the “color” of the surface. From now on, we’ll simply treat the color as a property of a surface and forget about light wavelengths.

## [Color Depth and Representation](https://gabrielgambetta.com/computer-graphics-from-scratch/01-common-concepts.html#color-depth-and-representation)

Monitors create colors by mixing different amounts of red, green, and blue. They do this by lighting the tiny colored dots on their surface at different intensities by supplying different voltages to them.

How many different intensities can we get? Although voltage is continuous, we’ll be manipulating colors with a computer, which uses discrete values (that is, a limited number of them). The more shades of red, green, and blue we can represent, the more colors we’ll be able to produce.

Most images you see these days use 8 bits per primary color, which we call a *color channel* in this context. Using 8 bits per channel gives us 24 bits per pixel, for a total of 224224 different colors (approximately 16.7 million). This format, known as *R8G8B8* or simply *888*, is the one we’ll use throughout this book. We say this format has a *color depth* of 24 bits.

This is by no means the only possible format. Not so long ago, in order to save memory, 15- and 16-bit formats were popular, assigning 5 bits per channel in the 15-bit case, and 5 bits for red, 6 for green, and 5 for blue in the 16-bit case (known as the *R5G6B5* or *565* format). Green gets the extra bit because our eyes are more sensitive to changes in green than to changes in red or blue.

With 16 bits, we get 216216 colors (approximately 65,000). This means you get one color for every 256 colors in 24-bit mode. Although 65,000 colors is plenty, for images where colors change very gradually you would be able to see very subtle “steps” that just aren’t visible with 16.7 million colors, where there are enough bits to represent the colors in between. For some specialized applications, such as color grading for movies, it’s a good idea to represent even more color detail, using even more bits per channel.

We’ll use 3 bytes to represent a color, each holding the value of an 8-bit color channel, from 0 to 255. We’ll express the colors as (R,G,B)(R,G,B)—for example, (255,0,0)(255,0,0) represents pure red; (255,255,255)(255,255,255) represents white; and (255,0,128)(255,0,128) represents a reddish purple.

## [Color Manipulation](https://gabrielgambetta.com/computer-graphics-from-scratch/01-common-concepts.html#color-manipulation)

We’ll use a handful of operations to manipulate colors. If you know some linear algebra, you can think of colors as vectors in 3D color space. If not, don’t worry, we’ll go through the basic operations we’ll be using now.

We can modify the intensity of a color by multiplying each of its color channels by a constant:



k(R,G,B)=(kR,kG,kB)k(R,G,B)=(kR,kG,kB)



For example, (32,0,128)(32,0,128) is twice as bright as (16,0,64)(16,0,64).

We can add two colors together by adding their color channels separately:



(R1,G1,B1)+(R2,G2,B2)=(R1+R2,G1+G2,B1+B2)(R1,G1,B1)+(R2,G2,B2)=(R1+R2,G1+G2,B1+B2)



For example, if we want to combine red (255,0,0)(255,0,0) and green (0,255,0)(0,255,0), we add them channel-wise and get (255,255,0)(255,255,0), which is yellow.

These operations can yield invalid values; for example, doubling the intensity of (192,64,32)(192,64,32) produces an RR value outside our color range. We’ll treat any value over 255 as 255, and any value below 0 as 0; we call this *clamping* the value to the [0–255] range. This is more or less equivalent to what happens when you take an under- or over-exposed photograph in real life: you get either completely black or completely white areas.

That about sums it up for our primer on colors and `PutPixel`. Before we move on to the next chapter, let’s spend a little time exploring how to represent the 3D objects we’ll be rendering.

## [The Scene](https://gabrielgambetta.com/computer-graphics-from-scratch/01-common-concepts.html#the-scene)

So far, we have introduced the canvas, the abstract surface on which we can color pixels. Now we turn our attention to the objects we’re interested in representing by introducing another abstraction: the *scene*.

The scene is the set of objects you may be interested in rendering. It could represent anything, from a single sphere floating in the empty infinity of space (we’ll start there) to an incredibly detailed model of the inside of a grumpy ogre’s nose.

We need a coordinate system to talk about objects within the scene. We can’t use the same coordinate system as the canvas, for two reasons. First, the canvas is 2D, whereas the scene is 3D. Second, the canvas and the scene use different units: we use pixels for the canvas and real-world units (such as the imperial or metric systems) for the scene.

The choice of axes is arbitrary, so we’ll pick something useful for our purposes. We’ll say that YY is up and XX and ZZ are horizontal, and all three axes are perpendicular to each other. Think of the plane XZXZ as the “floor,” while XYXY and YZYZ are vertical “walls” in a square room. This is consistent with the coordinate system we chose for the canvas, where YY is up and XX is horizontal. Figure 1-6 shows what this looks like.

![Figure 1-6: The coordinate system we’ll use for our scenes](https://gabrielgambetta.com/computer-graphics-from-scratch/images/coordinate-system.png)Figure 1-6: The coordinate system we’ll use for our scenes

The choice of scene units is somewhat arbitrary; it depends on what your scene represents. A measurement of “1” could mean 1 inch if you’re modeling a teacup, or it could mean 1 astronomical unit if you’re modeling the Solar System. As long as we use our chosen units consistently, it doesn’t matter what they are, so we can safely ignore them from now on.

## [Summary](https://gabrielgambetta.com/computer-graphics-from-scratch/01-common-concepts.html#summary)

In this chapter, we’ve introduced the canvas, an abstraction that represents a rectangular surface we can draw on, plus the one method we’ll build everything else on: `PutPixel`. We’ve also chosen a coordinate system to refer to the pixels on the canvas and described a way to represent the color of these pixels. Lastly, we introduced the concept of a scene and chose a coordinate system to use in the scene.

Having laid these foundations, it’s time to start building a raytracer and a rasterizer on top of them.