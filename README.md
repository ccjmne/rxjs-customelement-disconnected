# RxJs CustomElement Disconnected

Easy, drop-in solution to hook Custom Elements' disconnection lifecycle into RxJs pipelines.

Automatically cancel ("unsubscribe") from your subscription on `disconnectedCallback()`!

## BEFORE:

```typescript
class MyElement extends HTMLElement {

  private resizeSubscription: Subscription<any>;

  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
  }

  public connectedCallback(): void {
    this.resizeSubscription = fromEvent(window, 'resize').pipe(
      startWith(null),
    ).subscribe(() => console.log('resized'));
  }

  public disconnectedCallback(): void {
    this.resizeSubscription.unsubscribe();
  }

}

customElements.define('my-element', MyElement);
```

## AFTER:

```typescript
import { untilDisconnected } from 'rxjs-customelement-disconnected';

class MyElement extends HTMLElement {

  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
  }

  public connectedCallback(): void {
    fromEvent(window, 'resize').pipe(
      startWith(null),
      untilDisconnected(this), // 🤯
    ).subscribe(() => console.log('resized'));
  }

}

customElements.define('my-element', MyElement);
```

## DIFF:

```diff
+import { untilDisconnected } from 'rxjs-customelement-disconnected';
 
 class MyElement extends HTMLElement {
-
-  private resizeSubscription: Subscription<any>;
 
   constructor() {
     super();
     this.attachShadow({ mode: 'open' });
   }
 
   public connectedCallback(): void {
-    this.resizeSubscription = fromEvent(window, 'resize').pipe(
+    fromEvent(window, 'resize').pipe(
       startWith(null),
+      untilDisconnected(this), // 🤯
     ).subscribe(() => console.log('resized'));
   }
-
- public disconnectedCallback(): void {
-   this.resizeSubscription.unsubscribe();
- }

 }
 
 customElements.define('my-element', MyElement);
```
