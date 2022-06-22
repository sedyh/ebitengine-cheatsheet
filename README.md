# <img align="right" src="https://user-images.githubusercontent.com/19890545/150032502-2b114fdd-ca41-4b5d-9e01-a567f737aa2d.png" alt="ebiten-cheatsheet" title="ebiten-cheatsheet" /> Ebitengine Cheatsheet

Useful tips and snippets for Ebitengine.

### Contents

- [Relative crop](#relative-crop)
- [Center text](#center-text)
- [Flip Image](#flip-image)
- [Quick start](#quick-start)

### Relative Crop

<a href="#contents"><img src="https://user-images.githubusercontent.com/19890545/150034365-6561ab71-5cb4-466f-996c-ae4204ef7c12.png" alt="back" title="back" width="16px"/></a>
*If you try to make a sub image from another sub image, you may run into unusual behavior due to the fact that absolute coordinates are used for each crop, instead you can use this function so as not to constantly remember this nuance.*

```go
func RelativeCrop(source *ebiten.Image, r image.Rectangle) *ebiten.Image {
    rx, ry := source.Bounds().Min.X+r.Min.X, source.Bounds().Min.Y+r.Min.Y
    return source.SubImage(image.Rect(rx, ry, rx+r.Max.X, ry+r.Max.Y)).(*ebiten.Image)
}
```

### Center text
<a href="#contents"><img src="https://user-images.githubusercontent.com/19890545/150034365-6561ab71-5cb4-466f-996c-ae4204ef7c12.png" alt="back" title="back" width="16px"/></a>
*Text bounds behave differently than image bounds. To calculate center point you will need to subtract the minimum point and add half the size.*
```go
func DrawCenteredText(screen *ebiten.Image, font font.Face, s string, cx, cy int) {
    bounds := text.BoundString(font, s)
    x, y := cx-bounds.Min.X-bounds.Dx()/2, cy-bounds.Min.Y-bounds.Dy()/2
    text.Draw(screen, s, font, x, y, colornames.Red)
}
```


### Flip Image
<a href="#contents"><img src="https://user-images.githubusercontent.com/19890545/150034365-6561ab71-5cb4-466f-996c-ae4204ef7c12.png" alt="back" title="back" width="16px"/></a>
*Scale the image in the opposite direction by its size, and then move it into place.*
```go
func FlipHorizontal(source *ebiten.Image) *ebiten.Image {
    result := ebiten.NewImage(source.Size())
    op := &ebiten.DrawImageOptions{}
    op.GeoM.Scale(-1, 1)
    op.GeoM.Translate(float64(img.Bounds().Dx()), 0)
    return result.DrawImage(source, op)
}

func FlipVertical(source *ebiten.Image) *ebiten.Image {
    result := ebiten.NewImage(source.Size())
    op := &ebiten.DrawImageOptions{}
    op.GeoM.Scale(1, -1)
    op.GeoM.Translate(0, float64(img.Bounds().Dy()))
    return result.DrawImage(source, op)
}
```

### Quick start
<a href="#contents"><img src="https://user-images.githubusercontent.com/19890545/150034365-6561ab71-5cb4-466f-996c-ae4204ef7c12.png" alt="back" title="back" width="16px"/></a>
*Minimal game, vsync and resize are usually always helpful when debugging.*

```go
func main() {
	ebiten.SetWindowResizable(true)
	ebiten.SetFPSMode(ebiten.FPSModeVsyncOffMaximum)
	if err := ebiten.RunGame(NewGame()); err != nil {
		log.Fatal(err)
	}
}

type Game struct{}

func NewGame() *Game {
	return &Game{}
}

func (g *Game) Update() error {
	return nil
}

func (g *Game) Draw(screen *ebiten.Image) {
	screen.Fill(colornames.White)
}

func (g *Game) Layout(w, h int) (int, int) {
	return w, h
}
```
