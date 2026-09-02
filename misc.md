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

## marker pen bookmarklet

```javascript
javascript: (() => {
  if (window.__markerPenBookmarklet) return;
  window.__markerPenBookmarklet = 1;

  const style = document.createElement("style");
  style.textContent = `
    .mp-btn{position:fixed;bottom:20px;right:20px;width:44px;height:44px;border-radius:50%;background:#1f7a1f;color:#fff;border:none;font-size:20px;cursor:pointer;box-shadow:0 2px 10px rgba(0,0,0,.2);z-index:2147483647;display:flex;align-items:center;justify-content:center}
    .mp-btn:hover{background:#166016}
    .mp-panel{position:fixed;bottom:75px;right:20px;width:280px;background:#fff;border:1px solid #ddd;border-radius:10px;box-shadow:0 4px 20px rgba(0,0,0,.15);z-index:2147483647;display:none;flex-direction:column;overflow:hidden;font-family:system-ui,-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif}
    .mp-panel.open{display:flex}
    .mp-header{padding:8px 12px;background:#f6f8fa;border-bottom:1px solid #eee;font-weight:600;font-size:13px;display:flex;justify-content:space-between;align-items:center}
    .mp-header button{background:none;border:none;cursor:pointer;font-size:14px;color:#666}
    .mp-header button:hover{color:#024ea4}
    .mp-tools{display:flex;gap:8px;align-items:center;padding:10px 12px;border-bottom:1px solid #eee;font-size:12px;flex-wrap:wrap}
    .mp-swatch{width:22px;height:22px;border-radius:999px;border:2px solid transparent;cursor:pointer;padding:0;box-shadow:inset 0 0 0 1px rgba(0,0,0,.12)}
    .mp-swatch.active{border-color:#111827}
    .mp-red{background:#ef4444}.mp-black{background:#111111}.mp-blue{background:#2563eb}.mp-green{background:#16a34a}
    .mp-tools input[type="range"]{width:90px}
    .mp-overlay{position:fixed;inset:0;z-index:2147483646;pointer-events:none}
    .mp-overlay.active{pointer-events:auto;cursor:crosshair}
    .mp-overlay canvas{width:100%;height:100%;display:block;touch-action:none}
  `;
  document.head.appendChild(style);

  const root = document.createElement("div");
  root.innerHTML = `
    <button class="mp-btn" title="Marker Pen">✎</button>
    <div class="mp-panel">
      <div class="mp-header"><span>Marker Pen</span><div><button data-act="clear" title="Clear">&#128465;</button><button data-act="close" title="Close">&#10005;</button></div></div>
      <div class="mp-tools">
        <label>Size <input class="mp-size" type="range" min="1" max="18" value="4"></label>
        <div class="mp-palette"><button class="mp-swatch mp-red active" data-color="#ef4444" title="Red"></button><button class="mp-swatch mp-black" data-color="#111111" title="Black"></button><button class="mp-swatch mp-blue" data-color="#2563eb" title="Blue"></button><button class="mp-swatch mp-green" data-color="#16a34a" title="Green"></button></div>
        <button type="button" class="mp-mouse">Mouse</button>
      </div>
    </div>
    <div class="mp-overlay"><canvas></canvas></div>
  `;
  document.body.appendChild(root);

  const btn = root.querySelector(".mp-btn");
  const panel = root.querySelector(".mp-panel");
  const overlay = root.querySelector(".mp-overlay");
  const canvas = root.querySelector("canvas");
  const ctx = canvas.getContext("2d");
  const size = root.querySelector(".mp-size");
  const swatches = Array.from(root.querySelectorAll(".mp-swatch"));
  const mouse = root.querySelector(".mp-mouse");
  let active = false,
    drawing = false,
    color = "#ef4444",
    strokes = [],
    current = null;

  const key = () => `marker-pen:${location.pathname}${location.search}`;
  const save = () => localStorage.setItem(key(), JSON.stringify(strokes));
  const load = () => {
    try {
      strokes = JSON.parse(localStorage.getItem(key()) || "[]");
    } catch {
      strokes = [];
    }
    drawAll();
  };
  const resize = () => {
    const d = document.documentElement;
    canvas.width = Math.max(d.scrollWidth, d.clientWidth);
    canvas.height = Math.max(d.scrollHeight, d.clientHeight);
    overlay.style.width = canvas.width + "px";
    overlay.style.height = canvas.height + "px";
    drawAll();
  };
  const drawAll = () => {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.lineCap = "round";
    ctx.lineJoin = "round";
    strokes.forEach((s) => {
      if (!s.points || s.points.length < 2) return;
      ctx.strokeStyle = s.color;
      ctx.lineWidth = s.size;
      ctx.beginPath();
      ctx.moveTo(s.points[0].x, s.points[0].y);
      for (let i = 1; i < s.points.length; i++)
        ctx.lineTo(s.points[i].x, s.points[i].y);
      ctx.stroke();
    });
  };
  const setActive = (next) => {
    active = next;
    panel.classList.toggle("open", active);
    overlay.classList.toggle("active", active);
    mouse.classList.toggle("active", !active);
  };
  const pt = (e) => ({ x: e.pageX, y: e.pageY });
  const start = (e) => {
    if (!active) return;
    e.preventDefault();
    const p = pt(e);
    drawing = true;
    current = { color, size: +size.value, points: [p] };
  };
  const move = (e) => {
    if (!drawing || !active) return;
    e.preventDefault();
    const p = pt(e);
    current.points.push(p);
    const s = current;
    ctx.strokeStyle = s.color;
    ctx.lineWidth = s.size;
    const a = s.points[s.points.length - 2],
      b = s.points[s.points.length - 1];
    ctx.beginPath();
    ctx.moveTo(a.x, a.y);
    ctx.lineTo(b.x, b.y);
    ctx.stroke();
  };
  const end = () => {
    if (drawing && current && current.points.length > 1) {
      strokes.push(current);
      save();
    }
    drawing = false;
    current = null;
  };
  btn.onclick = () => setActive(!active);
  root.querySelector('[data-act="close"]').onclick = () => setActive(false);
  root.querySelector('[data-act="clear"]').onclick = () => {
    strokes = [];
    save();
    drawAll();
  };
  mouse.onclick = () => setActive(false);
  size.oninput = () => (ctx.lineWidth = +size.value);
  swatches.forEach(
    (s) =>
      (s.onclick = () => {
        color = s.dataset.color;
        swatches.forEach((x) => x.classList.toggle("active", x === s));
      }),
  );
  canvas.addEventListener("mousedown", start);
  canvas.addEventListener("mousemove", move);
  window.addEventListener("mouseup", end);
  canvas.addEventListener("mouseleave", end);
  window.addEventListener("resize", resize);
  window.addEventListener("scroll", resize, { passive: true });
  document.addEventListener("keydown", (e) => {
    if (
      (e.ctrlKey || e.metaKey) &&
      e.key.toLowerCase() === "z" &&
      active &&
      strokes.length
    ) {
      strokes.pop();
      save();
      drawAll();
      e.preventDefault();
    }
  });
  canvas.addEventListener(
    "touchstart",
    (e) => {
      const t = e.touches[0];
      if (!t) return;
      start({
        pageX: t.pageX,
        pageY: t.pageY,
        preventDefault: () => e.preventDefault(),
      });
    },
    { passive: false },
  );
  canvas.addEventListener(
    "touchmove",
    (e) => {
      const t = e.touches[0];
      if (!t) return;
      move({
        pageX: t.pageX,
        pageY: t.pageY,
        preventDefault: () => e.preventDefault(),
      });
    },
    { passive: false },
  );
  window.addEventListener("touchend", end);
  resize();
  load();
})();
```

## page-anchored marker pen bookmarklet

```javascript
javascript: (() => {
  if (window.__markerPenFixedBookmarklet) return;
  window.__markerPenFixedBookmarklet = 1;

  const style = document.createElement("style");
  style.textContent = `
    .mpf-btn{position:fixed;bottom:20px;right:20px;width:44px;height:44px;border-radius:50%;background:#1f7a1f;color:#fff;border:none;font-size:20px;cursor:pointer;box-shadow:0 2px 10px rgba(0,0,0,.2);z-index:2147483647;display:flex;align-items:center;justify-content:center}
    .mpf-btn:hover{background:#166016}
    .mpf-panel{position:fixed;bottom:75px;right:20px;width:280px;background:#fff;border:1px solid #ddd;border-radius:10px;box-shadow:0 4px 20px rgba(0,0,0,.15);z-index:2147483647;display:none;flex-direction:column;overflow:hidden;font-family:system-ui,-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif}
    .mpf-panel.open{display:flex}
    .mpf-header{padding:8px 12px;background:#f6f8fa;border-bottom:1px solid #eee;font-weight:600;font-size:13px;display:flex;justify-content:space-between;align-items:center}
    .mpf-header button{background:none;border:none;cursor:pointer;font-size:14px;color:#666}
    .mpf-header button:hover{color:#024ea4}
    .mpf-tools{display:flex;gap:8px;align-items:center;padding:10px 12px;border-bottom:1px solid #eee;font-size:12px;flex-wrap:wrap}
    .mpf-swatch{width:22px;height:22px;border-radius:999px;border:2px solid transparent;cursor:pointer;padding:0;box-shadow:inset 0 0 0 1px rgba(0,0,0,.12)}
    .mpf-swatch.active{border-color:#111827}
    .mpf-red{background:#ef4444}.mpf-black{background:#111111}.mpf-blue{background:#2563eb}.mpf-green{background:#16a34a}
    .mpf-tools input[type="range"]{width:90px}
    .mpf-overlay{position:absolute;top:0;left:0;z-index:2147483646;pointer-events:none}
    .mpf-overlay.active{pointer-events:auto;cursor:crosshair}
    .mpf-overlay canvas{width:100%;height:100%;display:block;touch-action:none}
  `;
  document.head.appendChild(style);

  const root = document.createElement("div");
  root.innerHTML = `
    <button class="mpf-btn" title="Marker Pen">✎</button>
    <div class="mpf-panel">
      <div class="mpf-header"><span>Marker Pen</span><div><button data-act="clear" title="Clear">&#128465;</button><button data-act="close" title="Close">&#10005;</button></div></div>
      <div class="mpf-tools">
        <label>Size <input class="mpf-size" type="range" min="1" max="18" value="4"></label>
        <div class="mpf-palette"><button class="mpf-swatch mpf-red active" data-color="#ef4444" title="Red"></button><button class="mpf-swatch mpf-black" data-color="#111111" title="Black"></button><button class="mpf-swatch mpf-blue" data-color="#2563eb" title="Blue"></button><button class="mpf-swatch mpf-green" data-color="#16a34a" title="Green"></button></div>
        <button type="button" class="mpf-mouse">Mouse</button>
      </div>
    </div>
    <div class="mpf-overlay"><canvas></canvas></div>
  `;
  document.body.appendChild(root);

  const btn = root.querySelector(".mpf-btn");
  const panel = root.querySelector(".mpf-panel");
  const overlay = root.querySelector(".mpf-overlay");
  const canvas = root.querySelector("canvas");
  const ctx = canvas.getContext("2d");
  const size = root.querySelector(".mpf-size");
  const swatches = Array.from(root.querySelectorAll(".mpf-swatch"));
  const mouse = root.querySelector(".mpf-mouse");
  let active = false,
    drawing = false,
    color = "#ef4444",
    strokes = [],
    current = null;

  const key = () => `marker-pen-page:${location.pathname}${location.search}`;
  const save = () => localStorage.setItem(key(), JSON.stringify(strokes));
  const load = () => {
    try {
      strokes = JSON.parse(localStorage.getItem(key()) || "[]");
    } catch {
      strokes = [];
    }
    drawAll();
  };
  const resize = () => {
    const d = document.documentElement;
    canvas.width = Math.max(d.scrollWidth, d.clientWidth);
    canvas.height = Math.max(d.scrollHeight, d.clientHeight);
    overlay.style.width = canvas.width + "px";
    overlay.style.height = canvas.height + "px";
    overlay.style.left = "0px";
    overlay.style.top = "0px";
    drawAll();
  };
  const drawAll = () => {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.lineCap = "round";
    ctx.lineJoin = "round";
    strokes.forEach((s) => {
      if (!s.points || s.points.length < 2) return;
      ctx.strokeStyle = s.color;
      ctx.lineWidth = s.size;
      ctx.beginPath();
      ctx.moveTo(s.points[0].x, s.points[0].y);
      for (let i = 1; i < s.points.length; i++)
        ctx.lineTo(s.points[i].x, s.points[i].y);
      ctx.stroke();
    });
  };
  const setActive = (next) => {
    active = next;
    panel.classList.toggle("open", active);
    overlay.classList.toggle("active", active);
    mouse.classList.toggle("active", !active);
  };
  const pt = (e) => ({ x: e.pageX, y: e.pageY });
  const start = (e) => {
    if (!active) return;
    e.preventDefault();
    const p = pt(e);
    drawing = true;
    current = { color, size: +size.value, points: [p] };
  };
  const move = (e) => {
    if (!drawing || !active) return;
    e.preventDefault();
    const p = pt(e);
    current.points.push(p);
    const s = current;
    ctx.strokeStyle = s.color;
    ctx.lineWidth = s.size;
    const a = s.points[s.points.length - 2],
      b = s.points[s.points.length - 1];
    ctx.beginPath();
    ctx.moveTo(a.x, a.y);
    ctx.lineTo(b.x, b.y);
    ctx.stroke();
  };
  const end = () => {
    if (drawing && current && current.points.length > 1) {
      strokes.push(current);
      save();
    }
    drawing = false;
    current = null;
  };
  btn.onclick = () => setActive(!active);
  root.querySelector('[data-act="close"]').onclick = () => setActive(false);
  root.querySelector('[data-act="clear"]').onclick = () => {
    strokes = [];
    save();
    drawAll();
  };
  mouse.onclick = () => setActive(false);
  size.oninput = () => (ctx.lineWidth = +size.value);
  swatches.forEach(
    (s) =>
      (s.onclick = () => {
        color = s.dataset.color;
        swatches.forEach((x) => x.classList.toggle("active", x === s));
      }),
  );
  canvas.addEventListener("mousedown", start);
  canvas.addEventListener("mousemove", move);
  window.addEventListener("mouseup", end);
  canvas.addEventListener("mouseleave", end);
  window.addEventListener("resize", resize);
  document.addEventListener("keydown", (e) => {
    if (
      (e.ctrlKey || e.metaKey) &&
      e.key.toLowerCase() === "z" &&
      active &&
      strokes.length
    ) {
      strokes.pop();
      save();
      drawAll();
      e.preventDefault();
    }
  });
  canvas.addEventListener(
    "touchstart",
    (e) => {
      const t = e.touches[0];
      if (!t) return;
      start({
        pageX: t.pageX,
        pageY: t.pageY,
        preventDefault: () => e.preventDefault(),
      });
    },
    { passive: false },
  );
  canvas.addEventListener(
    "touchmove",
    (e) => {
      const t = e.touches[0];
      if (!t) return;
      move({
        pageX: t.pageX,
        pageY: t.pageY,
        preventDefault: () => e.preventDefault(),
      });
    },
    { passive: false },
  );
  window.addEventListener("touchend", end);
  resize();
  load();
})();
```
