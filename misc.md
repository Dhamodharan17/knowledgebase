# scratchpad bookmarklet

```javascript
javascript: (() => {
  if (window.__spb) return;
  window.__spb = 1;
  const c = `.spb-btn{position:fixed;bottom:20px;right:20px;width:44px;height:44px;border-radius:50%;background:#0366d6;color:#fff;border:none;font-size:20px;cursor:pointer;box-shadow:0 2px 10px rgba(0,0,0,.2);z-index:2147483647;display:flex;align-items:center;justify-content:center}.spb-btn:hover{background:#024ea4}.spb-panel{position:fixed;bottom:75px;right:20px;width:320px;height:250px;min-width:200px;min-height:150px;background:#fff;border:1px solid #ddd;border-radius:10px;box-shadow:0 4px 20px rgba(0,0,0,.15);z-index:2147483647;display:none;flex-direction:column;overflow:hidden;font-family:system-ui,-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif}.spb-panel.open{display:flex}.spb-header{padding:8px 12px;background:#f6f8fa;border-bottom:1px solid #eee;font-weight:600;font-size:13px;display:flex;justify-content:space-between;align-items:center}.spb-header button{background:none;border:none;cursor:pointer;font-size:14px;color:#666}.spb-header button:hover{color:#024ea4}.spb-group{display:flex;gap:6px}.spb-ta{flex:1;border:none;padding:10px 12px;font-family:monospace;font-size:13px;resize:none;outline:none}.spb-edge{position:absolute;z-index:10}.spb-edge-n{top:-4px;left:8px;right:8px;height:8px;cursor:n-resize}.spb-edge-s{bottom:-4px;left:8px;right:8px;height:8px;cursor:s-resize}.spb-edge-e{right:-4px;top:8px;bottom:8px;width:8px;cursor:e-resize}.spb-edge-w{left:-4px;top:8px;bottom:8px;width:8px;cursor:w-resize}.spb-edge-nw{top:-4px;left:-4px;width:12px;height:12px;cursor:nw-resize}.spb-edge-ne{top:-4px;right:-4px;width:12px;height:12px;cursor:ne-resize}.spb-edge-sw{bottom:-4px;left:-4px;width:12px;height:12px;cursor:sw-resize}.spb-edge-se{bottom:-4px;right:-4px;width:12px;height:12px;cursor:se-resize}`;
  const s = document.createElement("style");
  s.textContent = c;
  document.head.appendChild(s);
  const r = document.createElement("div");
  r.innerHTML =
    '<button class="spb-btn" title="Scratchpad">&#9998;</button><div class="spb-panel"><div class="spb-edge spb-edge-n" data-dir="n"></div><div class="spb-edge spb-edge-s" data-dir="s"></div><div class="spb-edge spb-edge-e" data-dir="e"></div><div class="spb-edge spb-edge-w" data-dir="w"></div><div class="spb-edge spb-edge-nw" data-dir="nw"></div><div class="spb-edge spb-edge-ne" data-dir="ne"></div><div class="spb-edge spb-edge-sw" data-dir="sw"></div><div class="spb-edge spb-edge-se" data-dir="se"></div><div class="spb-header"><span>Scratchpad</span><div class="spb-group"><button data-a="c" title="Copy">&#128203;</button><button data-a="d" title="Download">&#11015;</button><button data-a="x" title="Clear">&#128465;</button></div></div><textarea class="spb-ta" placeholder="Type temporary notes here..."></textarea></div>';
  document.body.appendChild(r);
  const b = r.firstChild,
    p = r.children[1],
    t = p.querySelector(".spb-ta"),
    k = "scratchpad-bookmarklet";
  t.value = localStorage.getItem(k) || "";
  const save = () => localStorage.setItem(k, t.value);
  b.onclick = () => {
    p.classList.toggle("open");
    if (p.classList.contains("open")) t.focus();
  };
  r.querySelector("[data-a=x]").onclick = () => {
    t.value = "";
    save();
  };
  r.querySelector("[data-a=c]").onclick = () =>
    t.value && navigator.clipboard.writeText(t.value);
  r.querySelector("[data-a=d]").onclick = () => {
    if (!t.value) return;
    const bl = new Blob([t.value], { type: "text/markdown" }),
      a = document.createElement("a");
    a.href = URL.createObjectURL(bl);
    a.download = "scratchpad-notes.md";
    a.click();
    URL.revokeObjectURL(a.href);
  };
  t.oninput = save;
  t.onkeydown = (e) => {
    if (e.key !== "Enter") return;
    const s = t.selectionStart,
      e2 = t.selectionEnd,
      v = t.value,
      l = v.lastIndexOf("\n", s - 1) + 1,
      c = v.substring(l, s);
    if (c.startsWith("- ") || c.startsWith("* ")) {
      e.preventDefault();
      const pfx = c.substring(0, 2);
      if (c.trim() === "-" || c.trim() === "*") {
        t.value = v.substring(0, l) + v.substring(e2);
        t.selectionStart = t.selectionEnd = l;
      } else {
        t.value = v.substring(0, s) + "\\n" + pfx + v.substring(e2);
        t.selectionStart = t.selectionEnd = s + 3;
      }
      save();
    }
  };
  let mw = 200,
    mh = 150;
  p.querySelectorAll(".spb-edge").forEach(
    (e) =>
      (e.onmousedown = (o) => {
        o.preventDefault();
        const d = e.dataset.dir,
          rc = p.getBoundingClientRect(),
          sx = o.clientX,
          sy = o.clientY,
          sw = rc.width,
          sh = rc.height,
          sl = rc.left,
          st = rc.top,
          move = (m) => {
            const dx = m.clientX - sx,
              dy = m.clientY - sy;
            let nw = sw,
              nh = sh,
              nl = sl,
              nt = st;
            if (d.includes("e")) nw = Math.max(mw, sw + dx);
            if (d.includes("w")) {
              nw = Math.max(mw, sw - dx);
              nl = sl + (sw - nw);
            }
            if (d.includes("s")) nh = Math.max(mh, sh + dy);
            if (d.includes("n")) {
              nh = Math.max(mh, sh - dy);
              nt = st + (sh - nh);
            }
            p.style.width = nw + "px";
            p.style.height = nh + "px";
            p.style.left = nl + "px";
            p.style.top = nt + "px";
            p.style.right = "auto";
            p.style.bottom = "auto";
          },
          up = () => {
            document.removeEventListener("mousemove", move);
            document.removeEventListener("mouseup", up);
          };
        document.addEventListener("mousemove", move);
        document.addEventListener("mouseup", up);
      }),
  );
})();
```
