<script context="module" lang="ts">
    import { Canvas, CanvasView, LayoutAlignment, Paint, StaticLayout } from '@nativescript-community/ui-canvas';
    import type { NativeViewElementNode } from 'svelte-native/dom';
    import { conditionalEvent } from '@shared/utils/svelte/ui';
    // import { showToolTip } from '~/utils/utils';
    import { actionBarButtonHeight, colors, fonts } from '~/variables';
    const iconPaint = new Paint();
</script>

<script lang="ts">
    let { colorOnSurface, colorOnSurfaceVariant, colorPrimary } = $colors;
    $: ({ colorOnSurface, colorOnSurfaceVariant, colorPrimary } = $colors);
    $: iconPaint.fontFamily = $fonts.mdi;
    export let isVisible = true;
    export let isHidden = false;
    export let white = false;
    export let isEnabled = true;
    export let small = false;
    export let gray = false;
    export let isSelected = false;
    export let text = null;
    export let fontFamily = $fonts.mdi;
    export let selectedColor = white ? 'white' : undefined;
    export let color = null;
    export let onLongPress: Function = null;
    export let fontSize = 0;
    export let size: any = small ? 30 : $actionBarButtonHeight;
    export let tooltip = null;
    export let rounded = true;
    export let shape = null;
    export let height = null;
    export let width = null;

    let canvas: NativeViewElementNode<CanvasView>;

    // let actualColor = null;
    // $: actualColor = white ? 'white' : !isEnabled || gray ? colorOnSurfaceVariant : color;
    $: actualColor = color || (!isEnabled || gray ? colorOnSurfaceVariant : colorOnSurface);
    $: actualLongPress =
        onLongPress || tooltip
            ? (event) => {
                  if (event.ios && event.ios.state !== 3) {
                      return;
                  }
                  if (onLongPress) {
                      onLongPress(event);
                  } else {
                      //   showToolTip(tooltip);
                  }
              }
            : null;
    $: refresh(text);

    function refresh(...args) {
        canvas?.nativeView?.redraw();
    }
    function onCanvasDraw({ canvas, object }: { canvas: Canvas; object: CanvasView }) {
        iconPaint.fontFamily = fontFamily || $fonts.mdi;
        iconPaint.textSize = fontSize ? fontSize : small ? 16 : 24;
        iconPaint.color = isEnabled ? (isSelected ? selectedColor || colorPrimary : actualColor) : 'lightgray';
        const w = canvas.getWidth();
        const w2 = w / 2;
        const h2 = canvas.getHeight() / 2;
        const staticLayout = new StaticLayout(text, iconPaint, w, LayoutAlignment.ALIGN_CENTER, 1, 0, true);
        canvas.translate(0, h2 - staticLayout.getHeight() / 2);
        staticLayout.draw(canvas);
        // canvas.drawText(text, w2, w2+ textSize/3, iconPaint);
    }
</script>

<canvasview
    bind:this={canvas}
    borderRadius={shape === 'round' || (rounded && !shape) ? size / 2 : null}
    disableCss={true}
    rippleColor={actualColor}
    visibility={isVisible ? 'visible' : isHidden ? 'hidden' : 'collapse'}
    on:draw={onCanvasDraw}
    {...$$restProps}
    height={height || size}
    width={width || size}
    on:tap
    use:conditionalEvent={{ condition: !!actualLongPress, event: 'longPress', callback: actualLongPress }} />
<!-- <mdbutton
    {isEnabled}
    {text}
    variant="text"
    shape={shape || (rounded ? 'round' : null)}
    disableCss={true}
    rippleColor={actualColor}
    {fontFamily}
    visibility={isVisible ? 'visible' : isHidden ? 'hidden' : 'collapse'}
    color={isSelected ? selectedColor : actualColor}
    {...$$restProps}
    on:tap
    on:longPress={actualLongPress}
    width={width || size}
    height={height || size}
    fontSize={fontSize ? fontSize : small ? 16 : 24}
/> -->
