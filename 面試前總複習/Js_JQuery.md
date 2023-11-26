# prop vs attr

---

在 jQuery 中，.prop() 和 .attr() 是兩種用來取得屬性值的方法，針對不同的情況有著不同的作用。

> **prop ( ) :**

針對`DOM`屬性（properties）。它用於取得目前DOM元素的特定屬性，例如 checkbox 或 radio 的 checked 屬性，對於輸入框的 value 屬性等。 
`$("#userName").prop('value')` 取得的是目前輸入框的值，與使用者即時輸入的內容保持同步。

> **attr ( ) :**

用來取得HTML屬性值。它取得的是`HTML`屬性中的值，而不一定是即時的或目前的屬性值。它不會反映最新的屬性狀態，而是傳回HTML標記中定義的屬性值。
所以，`$("#userName").attr('value')` 取得的是在`HTML`標籤中定義的屬性值。
例如 `<input value="預設值" /> `中的 "預設值"，不會隨使用者輸入而改變。

<br>

> `.prop('value')` 更適合用於取得使用者即時輸入的內容（目前值） 
> `.attr('value')` 取得的是HTML標籤中初始設定的值。