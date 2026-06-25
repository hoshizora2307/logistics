import React, { useState, useEffect, useRef, useCallback } from "react";

const STORAGE_KEY = "suppliers-v1";
const HQ_ADDRESS = "長野県大町市大町1903-1";
const HQ_LABEL = "本社（大町市大町1903-1）";

const COLORS = {
  bg: "#F7F4EC",
  ink: "#1F2E26",
  inkSoft: "#3E5246",
  accent: "#D98E2B",
  accentSoft: "#F0D9AE",
  line: "#DAD3C2",
  card: "#FFFFFF",
  danger: "#B3452F",
};

const CATEGORY_OPTIONS = [
  { key: "egg", label: "鶏卵・卵製品" },
  { key: "vegetable", label: "野菜・青果" },
  { key: "pickle", label: "漬物・ザーサイ等" },
  { key: "meat", label: "肉・加工品" },
  { key: "seafood", label: "魚・海産物" },
  { key: "dairy", label: "乳製品" },
  { key: "grain", label: "米・穀物" },
  { key: "other", label: "その他" },
];

const categoryLabel = (key) =>
  CATEGORY_OPTIONS.find((c) => c.key === key)?.label || "その他";

const categoryColor = (key) => {
  const map = {
    egg: "#D98E2B",
    vegetable: "#5B8C5A",
    pickle: "#8C6BB1",
    meat: "#B3452F",
    seafood: "#3E7C9E",
    dairy: "#C9A227",
    grain: "#A47C48",
    other: "#6B6B6B",
  };
  return map[key] || "#6B6B6B";
};

function uid() {
  return Date.now().toString(36) + Math.random().toString(36).slice(2, 8);
}

function resizeImageFile(file, maxSize = 800, quality = 0.75) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = (e) => {
      const img = new Image();
      img.onload = () => {
        let { width, height } = img;
        if (width > height && width > maxSize) {
          height = Math.round((height * maxSize) / width);
          width = maxSize;
        } else if (height > maxSize) {
          width = Math.round((width * maxSize) / height);
          height = maxSize;
        }
        const canvas = document.createElement("canvas");
        canvas.width = width;
        canvas.height = height;
        const ctx = canvas.getContext("2d");
        ctx.drawImage(img, 0, 0, width, height);
        resolve(canvas.toDataURL("image/jpeg", quality));
      };
      img.onerror = reject;
      img.src = e.target.result;
    };
    reader.onerror = reject;
    reader.readAsDataURL(file);
  });
}

let _tfLoadPromise = null;
function loadTF() {
  if (window.tf && window.mobilenet) return Promise.resolve();
  if (_tfLoadPromise) return _tfLoadPromise;
  _tfLoadPromise = new Promise((resolve, reject) => {
    const s1 = document.createElement("script");
    s1.src = "https://cdnjs.cloudflare.com/ajax/libs/tensorflow/4.17.0/tf.min.js";
    s1.onload = () => {
      const s2 = document.createElement("script");
      s2.src =
        "https://cdn.jsdelivr.net/npm/@tensorflow-models/mobilenet@2.1.1/dist/mobilenet.min.js";
      s2.onload = () => resolve();
      s2.onerror = reject;
      document.head.appendChild(s2);
    };
    s1.onerror = reject;
    document.head.appendChild(s1);
  });
  return _tfLoadPromise;
}

let _mobilenetModel = null;
async function getMobilenetModel() {
  await loadTF();
  if (!_mobilenetModel) {
    _mobilenetModel = await window.mobilenet.load({ version: 2, alpha: 1.0 });
  }
  return _mobilenetModel;
}

function embedFromDataUrl(dataUrl) {
  return new Promise((resolve, reject) => {
    const img = new Image();
    img.crossOrigin = "anonymous";
    img.onload = async () => {
      try {
        const model = await getMobilenetModel();
        const embedding = model.infer(img, true);
        const arr = await embedding.data();
        embedding.dispose();
        resolve(Array.from(arr));
      } catch (e) {
        reject(e);
      }
    };
    img.onerror = reject;
    img.src = dataUrl;
  });
}

function cosineSimilarity(a, b) {
  if (!a || !b || a.length !== b.length) return -1;
  let dot = 0, na = 0, nb = 0;
  for (let i = 0; i < a.length; i++) {
    dot += a[i] * b[i];
    na += a[i] * a[i];
    nb += b[i] * b[i];
  }
  if (na === 0 || nb === 0) return -1;
  return dot / (Math.sqrt(na) * Math.sqrt(nb));
}

async function geocodeAddress(address) {
  const url = `https://nominatim.openstreetmap.org/search?format=json&limit=1&countrycodes=jp&q=${encodeURIComponent(
    address
  )}`;
  const res = await fetch(url, {
    headers: { "Accept-Language": "ja" },
  });
  if (!res.ok) throw new Error("ジオコーディングに失敗しました");
  const data = await res.json();
  if (!data || data.length === 0) return null;
  return { lat: parseFloat(data[0].lat), lng: parseFloat(data[0].lon) };
}

function haversine(a, b) {
  const R = 6371;
  const dLat = ((b.lat - a.lat) * Math.PI) / 180;
  const dLng = ((b.lng - a.lng) * Math.PI) / 180;
  const lat1 = (a.lat * Math.PI) / 180;
  const lat2 = (b.lat * Math.PI) / 180;
  const sin1 = Math.sin(dLat / 2);
  const sin2 = Math.sin(dLng / 2);
  const c =
    sin1 * sin1 + Math.cos(lat1) * Math.cos(lat2) * sin2 * sin2;
  return 2 * R * Math.asin(Math.sqrt(c));
}

function buildRoute(start, stops) {
  const remaining = [...stops];
  const order = [];
  let current = start;
  while (remaining.length > 0) {
    let bestIdx = 0;
    let bestDist = Infinity;
    remaining.forEach((s, i) => {
      const d = haversine(current, s);
      if (d < bestDist) {
        bestDist = d;
        bestIdx = i;
      }
    });
    const next = remaining.splice(bestIdx, 1)[0];
    order.push({ ...next, legDistanceKm: bestDist });
    current = next;
  }
  return order;
}

const SEED_SUPPLIERS = [
  {
    id: uid(),
    name: "信州たまご農場",
    category: "egg",
    address: "長野県大町市常盤7000",
    phone: "0261-22-0000",
    hours: "8:00-17:00",
    closedDay: "日曜",
    contact: "田中 様",
    website: "",
    lat: 36.498,
    lng: 137.853,
    products: [],
    notes: "鶏卵を定期仕入れ。冷蔵車で集荷。",
  },
  {
    id: uid(),
    name: "ユーパレット",
    category: "pickle",
    address: "長野県松本市大字今井3000",
    phone: "0263-00-0000",
    hours: "9:00-18:00",
    closedDay: "水曜",
    contact: "佐藤 様",
    website: "",
    lat: 36.178,
    lng: 137.95,
    products: [],
    notes: "ザーサイなど中華系漬物を仕入れ。",
  },
];

export default function SupplierApp() {
  const [suppliers, setSuppliers] = useState([]);
  const [loaded, setLoaded] = useState(false);
  const [view, setView] = useState("map");
  const [selectedId, setSelectedId] = useState(null);
  const [showForm, setShowForm] = useState(false);
  const [editTarget, setEditTarget] = useState(null);
  const [filterCat, setFilterCat] = useState("all");
  const [routeStops, setRouteStops] = useState([]);
  const [routeResult, setRouteResult] = useState(null);
  const [routeStart, setRouteStart] = useState(HQ_ADDRESS);
  const [routeStartCoord, setRouteStartCoord] = useState(null);
  const [routeLoading, setRouteLoading] = useState(false);
  const [toast, setToast] = useState(null);

  const mapRef = useRef(null);
  const leafletMapRef = useRef(null);
  const markersRef = useRef([]);
  const routeLineRef = useRef(null);
  const leafletLoadedRef = useRef(false);

  useEffect(() => {
    (async () => {
      try {
        const res = await window.storage.get(STORAGE_KEY);
        if (res && res.value) {
          setSuppliers(JSON.parse(res.value));
        } else {
          setSuppliers(SEED_SUPPLIERS);
          await window.storage.set(STORAGE_KEY, JSON.stringify(SEED_SUPPLIERS));
        }
      } catch (e) {
        setSuppliers(SEED_SUPPLIERS);
      } finally {
        setLoaded(true);
      }
    })();
  }, []);

  const persist = useCallback(async (next) => {
    setSuppliers(next);
    try {
      await window.storage.set(STORAGE_KEY, JSON.stringify(next));
    } catch (e) {
      console.error("保存に失敗しました", e);
    }
  }, []);

  const showToast = (msg) => {
    setToast(msg);
    setTimeout(() => setToast(null), 2400);
  };

  useEffect(() => {
    if (leafletLoadedRef.current) return;
    leafletLoadedRef.current = true;
    const css = document.createElement("link");
    css.rel = "stylesheet";
    css.href = "https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.css";
    document.head.appendChild(css);
    const script = document.createElement("script");
    script.src = "https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.js";
    script.onload = () => setMapReadyTick((t) => t + 1);
    document.head.appendChild(script);
  }, []);
  const [, setMapReadyTick] = useState(0);

  useEffect(() => {
    if (view !== "map" && view !== "route") return;
    if (!window.L || !mapRef.current) return;
    if (leafletMapRef.current) {
      setTimeout(() => leafletMapRef.current.invalidateSize(), 50);
      return;
    }
    const map = window.L.map(mapRef.current, {
      center: [36.35, 137.9],
      zoom: 9,
      zoomControl: true,
    });
    window.L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
      attribution: "&copy; OpenStreetMap contributors",
      maxZoom: 19,
    }).addTo(map);
    leafletMapRef.current = map;
    setTimeout(() => map.invalidateSize(), 50);
  }, [view, suppliers.length]);

  const renderMarkers = useCallback(
    (list) => {
      if (!window.L || !leafletMapRef.current) return;
      markersRef.current.forEach((m) => m.remove());
      markersRef.current = [];
      list.forEach((s) => {
        if (typeof s.lat !== "number" || typeof s.lng !== "number") return;
        const color = categoryColor(s.category);
        const icon = window.L.divIcon({
          className: "",
          html: `<div style="
            width:34px;height:34px;border-radius:10px;
            background:${color};
            border:2px solid #FFFFFF;
            box-shadow:0 2px 6px rgba(0,0,0,0.35);
            display:flex;align-items:center;justify-content:center;
            color:white;font-size:14px;font-weight:700;
            transform: rotate(-3deg);
          ">${s.name.slice(0, 1)}</div>`,
          iconSize: [34, 34],
          iconAnchor: [17, 17],
        });
        const marker = window.L.marker([s.lat, s.lng], { icon }).addTo(
          leafletMapRef.current
        );
        marker.bindPopup(
          `<div style="font-family:sans-serif;min-width:160px">
            <div style="font-weight:700;margin-bottom:4px">${s.name}</div>
            <div style="font-size:12px;color:#555">${categoryLabel(
              s.category
            )}</div>
            <div style="font-size:12px;margin-top:4px">${s.address || ""}</div>
          </div>`
        );
        marker.on("click", () => setSelectedId(s.id));
        markersRef.current.push(marker);
      });
    },
    []
  );

  useEffect(() => {
    if (view !== "map") return;
    const filtered =
      filterCat === "all"
        ? suppliers
        : suppliers.filter((s) => s.category === filterCat);
    renderMarkers(filtered);
  }, [view, suppliers, filterCat, renderMarkers]);

  useEffect(() => {
    if (view !== "route") return;
    if (routeStartCoord) return;
    if (routeStart !== HQ_ADDRESS) return;
    (async () => {
      try {
        const coord = await geocodeAddress(HQ_ADDRESS);
        if (coord) setRouteStartCoord(coord);
      } catch (e) {

      }
    })();
  }, [view, routeStart, routeStartCoord]);

  useEffect(() => {
    if (view !== "route" || !routeResult || !window.L || !leafletMapRef.current)
      return;
    markersRef.current.forEach((m) => m.remove());
    markersRef.current = [];
    if (routeLineRef.current) {
      routeLineRef.current.remove();
      routeLineRef.current = null;
    }
    const points = [routeStartCoord, ...routeResult.map((r) => ({ lat: r.lat, lng: r.lng }))];
    const latlngs = points.map((p) => [p.lat, p.lng]);
    routeLineRef.current = window.L
      .polyline(latlngs, { color: COLORS.accent, weight: 4, dashArray: "6 6" })
      .addTo(leafletMapRef.current);

    const startIcon = window.L.divIcon({
      className: "",
      html: `<div style="width:30px;height:30px;border-radius:50%;background:${COLORS.ink};border:2px solid white;display:flex;align-items:center;justify-content:center;color:white;font-size:12px;font-weight:700;box-shadow:0 2px 6px rgba(0,0,0,0.35)">出発</div>`,
      iconSize: [30, 30],
      iconAnchor: [15, 15],
    });
    const m0 = window.L.marker([routeStartCoord.lat, routeStartCoord.lng], {
      icon: startIcon,
    }).addTo(leafletMapRef.current);
    markersRef.current.push(m0);

    routeResult.forEach((s, idx) => {
      const icon = window.L.divIcon({
        className: "",
        html: `<div style="width:32px;height:32px;border-radius:10px;background:${categoryColor(
          s.category
        )};border:2px solid white;display:flex;align-items:center;justify-content:center;color:white;font-size:14px;font-weight:700;box-shadow:0 2px 6px rgba(0,0,0,0.35)">${idx + 1}</div>`,
        iconSize: [32, 32],
        iconAnchor: [16, 16],
      });
      const marker = window.L.marker([s.lat, s.lng], { icon }).addTo(
        leafletMapRef.current
      );
      marker.bindPopup(
        `<div style="font-family:sans-serif"><b>${idx + 1}. ${s.name}</b><br/>${s.address || ""}</div>`
      );
      markersRef.current.push(marker);
    });

    leafletMapRef.current.fitBounds(latlngs, { padding: [40, 40] });
  }, [view, routeResult, routeStartCoord]);

  const handleSave = async (supplierData) => {
    let lat = supplierData.lat;
    let lng = supplierData.lng;
    if ((lat == null || lng == null) && supplierData.address) {
      try {
        const coord = await geocodeAddress(supplierData.address);
        if (coord) {
          lat = coord.lat;
          lng = coord.lng;
        } else {
          showToast("住所から位置情報を取得できませんでした。手動で調整してください。");
        }
      } catch (e) {
        showToast("ジオコーディングに失敗しました。通信環境をご確認ください。");
      }
    }
    const record = { ...supplierData, lat, lng };
    let next;
    if (editTarget) {
      next = suppliers.map((s) => (s.id === editTarget.id ? { ...record, id: s.id } : s));
    } else {
      next = [...suppliers, { ...record, id: uid() }];
    }
    await persist(next);
    setShowForm(false);
    setEditTarget(null);
    showToast(editTarget ? "仕入先情報を更新しました" : "仕入先を登録しました");
  };

  const handleDelete = async (id) => {
    if (!window.confirm("この仕入先を削除しますか？")) return;
    await persist(suppliers.filter((s) => s.id !== id));
    if (selectedId === id) setSelectedId(null);
    showToast("削除しました");
  };

  const calculateRoute = async () => {
    if (routeStops.length === 0) {
      showToast("回る仕入先を1件以上選んでください");
      return;
    }
    setRouteLoading(true);
    try {
      let startCoord = routeStartCoord;
      if (!startCoord) {
        if (!routeStart.trim()) {
          showToast("出発地点を入力してください");
          setRouteLoading(false);
          return;
        }
        const coord = await geocodeAddress(routeStart);
        if (!coord) {
          showToast("出発地点の位置を特定できませんでした");
          setRouteLoading(false);
          return;
        }
        startCoord = coord;
        setRouteStartCoord(coord);
      }
      const stops = suppliers.filter((s) => routeStops.includes(s.id));
      const order = buildRoute(startCoord, stops);
      setRouteResult(order);
    } finally {
      setRouteLoading(false);
    }
  };

  const totalDistance = routeResult
    ? routeResult.reduce((sum, r) => sum + r.legDistanceKm, 0)
    : 0;

  const allProducts = suppliers.flatMap((s) =>
    (s.products || []).map((p) => ({ ...p, supplierName: s.name, supplierId: s.id }))
  );

  const filteredSuppliers =
    filterCat === "all" ? suppliers : suppliers.filter((s) => s.category === filterCat);

  if (!loaded) {
    return (
      <div style={{ ...styles.app, display: "flex", alignItems: "center", justifyContent: "center" }}>
        <div style={{ color: COLORS.inkSoft }}>読み込み中…</div>
      </div>
    );
  }

  return (
    <div style={styles.app}>
      <style>{`
        * { box-sizing: border-box; }
        .leaflet-popup-content-wrapper { border-radius: 10px; }
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-thumb { background: ${COLORS.line}; border-radius: 4px; }
      `}</style>

      <Header view={view} setView={setView} onAdd={() => { setEditTarget(null); setShowForm(true); }} />

      <div style={styles.body}>
        {view === "map" && (
          <>
            <Sidebar
              suppliers={filteredSuppliers}
              filterCat={filterCat}
              setFilterCat={setFilterCat}
              selectedId={selectedId}
              setSelectedId={setSelectedId}
              onEdit={(s) => { setEditTarget(s); setShowForm(true); }}
              onDelete={handleDelete}
            />
            <div style={styles.mapArea}>
              <div ref={mapRef} style={{ width: "100%", height: "100%" }} />
            </div>
          </>
        )}

        {view === "list" && (
          <ListView
            suppliers={suppliers}
            onEdit={(s) => { setEditTarget(s); setShowForm(true); }}
            onDelete={handleDelete}
          />
        )}

        {view === "photos" && <PhotoGallery products={allProducts} suppliers={suppliers} />}

        {view === "camera" && <CameraMatchView suppliers={suppliers} allProducts={allProducts} />}

        {view === "route" && (
          <RouteView
            suppliers={suppliers}
            routeStops={routeStops}
            setRouteStops={setRouteStops}
            routeStart={routeStart}
            setRouteStart={setRouteStart}
            routeStartCoord={routeStartCoord}
            setRouteStartCoord={setRouteStartCoord}
            calculateRoute={calculateRoute}
            routeLoading={routeLoading}
            routeResult={routeResult}
            totalDistance={totalDistance}
            mapRef={mapRef}
          />
        )}
      </div>

      {showForm && (
        <SupplierFormModal
          initial={editTarget}
          onClose={() => { setShowForm(false); setEditTarget(null); }}
          onSave={handleSave}
        />
      )}

      {toast && <div style={styles.toast}>{toast}</div>}

      <div style={styles.footer}>
        © {new Date().getFullYear()} MONDOCOLO Co., Ltd. All rights reserved.
      </div>
    </div>
  );
}

function Header({ view, setView, onAdd }) {
  const tabs = [
    { key: "map", label: "地図" },
    { key: "list", label: "一覧" },
    { key: "photos", label: "商品写真" },
    { key: "camera", label: "📷 カメラ照合" },
    { key: "route", label: "ルート提案" },
  ];
  return (
    <div style={styles.header}>
      <div style={styles.brand}>
        <div style={styles.brandMark}>信</div>
        <div>
          <div style={styles.brandTitle}>仕入先マップ</div>
          <div style={styles.brandSub}>信州マルシェ 業者管理</div>
        </div>
      </div>
      <div style={styles.tabs}>
        {tabs.map((t) => (
          <button
            key={t.key}
            onClick={() => setView(t.key)}
            style={{
              ...styles.tabBtn,
              ...(view === t.key ? styles.tabBtnActive : {}),
            }}
          >
            {t.label}
          </button>
        ))}
      </div>
      <button style={styles.addBtn} onClick={onAdd}>
        ＋ 仕入先を登録
      </button>
    </div>
  );
}

function Sidebar({ suppliers, filterCat, setFilterCat, selectedId, setSelectedId, onEdit, onDelete }) {
  return (
    <div style={styles.sidebar}>
      <div style={styles.filterRow}>
        <select
          value={filterCat}
          onChange={(e) => setFilterCat(e.target.value)}
          style={styles.select}
        >
          <option value="all">すべての品目</option>
          {CATEGORY_OPTIONS.map((c) => (
            <option key={c.key} value={c.key}>
              {c.label}
            </option>
          ))}
        </select>
      </div>
      <div style={styles.sidebarList}>
        {suppliers.length === 0 && (
          <div style={styles.emptyState}>
            該当する仕入先がありません。右上の「＋ 仕入先を登録」から追加してください。
          </div>
        )}
        {suppliers.map((s) => (
          <div
            key={s.id}
            onClick={() => setSelectedId(s.id)}
            style={{
              ...styles.supplierCard,
              ...(selectedId === s.id ? styles.supplierCardActive : {}),
            }}
          >
            <div style={styles.cardTopRow}>
              <span
                style={{
                  ...styles.catChip,
                  background: categoryColor(s.category) + "22",
                  color: categoryColor(s.category),
                }}
              >
                {categoryLabel(s.category)}
              </span>
            </div>
            <div style={styles.cardName}>{s.name}</div>
            <div style={styles.cardMeta}>{s.address}</div>
            <div style={styles.cardMeta}>
              {s.phone} {s.hours ? `・${s.hours}` : ""}
            </div>
            {s.contact && <div style={styles.cardMeta}>担当: {s.contact}</div>}
            <div style={styles.cardActions}>
              {s.website && (
                <a
                  href={s.website}
                  target="_blank"
                  rel="noreferrer"
                  style={styles.cardLink}
                  onClick={(e) => e.stopPropagation()}
                >
                  HP ↗
                </a>
              )}
              <button
                style={styles.cardSmallBtn}
                onClick={(e) => { e.stopPropagation(); onEdit(s); }}
              >
                編集
              </button>
              <button
                style={{ ...styles.cardSmallBtn, color: COLORS.danger }}
                onClick={(e) => { e.stopPropagation(); onDelete(s.id); }}
              >
                削除
              </button>
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}

function ListView({ suppliers, onEdit, onDelete }) {
  return (
    <div style={styles.listWrap}>
      <table style={styles.table}>
        <thead>
          <tr>
            {["名前", "品目", "住所", "電話番号", "営業時間", "定休日", "担当者", "HP", "操作"].map((h) => (
              <th key={h} style={styles.th}>{h}</th>
            ))}
          </tr>
        </thead>
        <tbody>
          {suppliers.map((s) => (
            <tr key={s.id} style={styles.tr}>
              <td style={styles.td}><b>{s.name}</b></td>
              <td style={styles.td}>
                <span style={{ ...styles.catChip, background: categoryColor(s.category) + "22", color: categoryColor(s.category) }}>
                  {categoryLabel(s.category)}
                </span>
              </td>
              <td style={styles.td}>{s.address}</td>
              <td style={styles.td}>{s.phone}</td>
              <td style={styles.td}>{s.hours}</td>
              <td style={styles.td}>{s.closedDay}</td>
              <td style={styles.td}>{s.contact}</td>
              <td style={styles.td}>
                {s.website ? <a href={s.website} target="_blank" rel="noreferrer" style={styles.cardLink}>開く↗</a> : "—"}
              </td>
              <td style={styles.td}>
                <button style={styles.cardSmallBtn} onClick={() => onEdit(s)}>編集</button>
                <button style={{ ...styles.cardSmallBtn, color: COLORS.danger }} onClick={() => onDelete(s.id)}>削除</button>
              </td>
            </tr>
          ))}
          {suppliers.length === 0 && (
            <tr><td style={styles.td} colSpan={9}>仕入先が登録されていません。</td></tr>
          )}
        </tbody>
      </table>
    </div>
  );
}

function PhotoGallery({ products, suppliers }) {
  const [filterSupplier, setFilterSupplier] = useState("all");
  const filtered = filterSupplier === "all" ? products : products.filter((p) => p.supplierId === filterSupplier);

  return (
    <div style={styles.galleryWrap}>
      <div style={styles.filterRow}>
        <select value={filterSupplier} onChange={(e) => setFilterSupplier(e.target.value)} style={styles.select}>
          <option value="all">すべての仕入先</option>
          {suppliers.map((s) => (
            <option key={s.id} value={s.id}>{s.name}</option>
          ))}
        </select>
        <div style={{ color: COLORS.inkSoft, fontSize: 13 }}>
          倉庫で在庫切れの商品があれば、ここで見た目を確認できます。
        </div>
      </div>
      {filtered.length === 0 ? (
        <div style={styles.emptyState}>
          まだ商品写真が登録されていません。仕入先の編集画面から商品写真を追加してください。
        </div>
      ) : (
        <div style={styles.photoGrid}>
          {filtered.map((p, idx) => (
            <div key={idx} style={styles.photoCard}>
              <img src={p.image} alt={p.name} style={styles.photoImg} />
              <div style={styles.photoCaption}>
                <div style={styles.photoName}>{p.name}</div>
                <div style={styles.photoSupplier}>{p.supplierName}</div>
              </div>
            </div>
          ))}
        </div>
      )}
    </div>
  );
}

function CameraMatchView({ suppliers, allProducts }) {
  const videoRef = useRef(null);
  const streamRef = useRef(null);
  const [cameraOn, setCameraOn] = useState(false);
  const [cameraError, setCameraError] = useState(null);
  const [modelLoading, setModelLoading] = useState(false);
  const [analyzing, setAnalyzing] = useState(false);
  const [capturedImage, setCapturedImage] = useState(null);
  const [matches, setMatches] = useState(null);

  const registeredCount = allProducts.filter((p) => p.embedding).length;

  const startCamera = async () => {
    setCameraError(null);
    setMatches(null);
    setCapturedImage(null);
    try {
      const stream = await navigator.mediaDevices.getUserMedia({
        video: { facingMode: "environment" },
        audio: false,
      });
      streamRef.current = stream;
      setCameraOn(true);
      setTimeout(() => {
        if (videoRef.current) videoRef.current.srcObject = stream;
      }, 0);
    } catch (e) {
      setCameraError(
        "カメラを起動できませんでした。ブラウザのカメラ許可設定をご確認ください。"
      );
    }
  };

  const stopCamera = () => {
    if (streamRef.current) {
      streamRef.current.getTracks().forEach((t) => t.stop());
      streamRef.current = null;
    }
    setCameraOn(false);
  };

  useEffect(() => {
    return () => stopCamera();
  }, []);

  const capture = async () => {
    if (!videoRef.current) return;
    const video = videoRef.current;
    const canvas = document.createElement("canvas");
    canvas.width = video.videoWidth;
    canvas.height = video.videoHeight;
    const ctx = canvas.getContext("2d");
    ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
    const dataUrl = canvas.toDataURL("image/jpeg", 0.85);
    setCapturedImage(dataUrl);
    stopCamera();
    await runMatch(dataUrl);
  };

  const handleFileCapture = async (e) => {
    const file = e.target.files?.[0];
    if (!file) return;
    const dataUrl = await resizeImageFile(file, 700, 0.8);
    setCapturedImage(dataUrl);
    setMatches(null);
    await runMatch(dataUrl);
    e.target.value = "";
  };

  const runMatch = async (dataUrl) => {
    const candidates = allProducts.filter((p) => p.embedding);
    if (candidates.length === 0) {
      setMatches([]);
      return;
    }
    setAnalyzing(true);
    setModelLoading(true);
    try {
      const targetEmbedding = await embedFromDataUrl(dataUrl);
      setModelLoading(false);
      const scored = candidates
        .map((p) => ({
          ...p,
          score: cosineSimilarity(targetEmbedding, p.embedding),
        }))
        .sort((a, b) => b.score - a.score)
        .slice(0, 3);
      setMatches(scored);
    } catch (e) {
      setMatches([]);
      setCameraError("画像の解析に失敗しました。もう一度お試しください。");
    } finally {
      setAnalyzing(false);
      setModelLoading(false);
    }
  };

  const reset = () => {
    setCapturedImage(null);
    setMatches(null);
    setCameraError(null);
  };

  const supplierOf = (supplierId) => suppliers.find((s) => s.id === supplierId);

  return (
    <div style={styles.cameraWrap}>
      <div style={styles.cameraPanel}>
        <div style={styles.routeSectionTitle}>商品をカメラで照合</div>
        <div style={styles.cameraDesc}>
          倉庫にある商品をカメラに映すと、登録済みの商品写真と見た目を比較して
          発注先の候補を提示します（端末内だけで処理され、画像は送信されません）。
        </div>

        {registeredCount === 0 && (
          <div style={styles.cameraNotice}>
            まだ照合できる商品写真が登録されていません。先に「商品写真」タブの
            仕入先編集画面から写真を登録してください。
          </div>
        )}

        {!capturedImage && (
          <div style={styles.cameraStage}>
            {cameraOn ? (
              <video ref={videoRef} autoPlay playsInline style={styles.cameraVideo} />
            ) : (
              <div style={styles.cameraPlaceholder}>
                カメラを起動するか、写真を選んで照合できます
              </div>
            )}
          </div>
        )}

        {capturedImage && (
          <div style={styles.cameraStage}>
            <img src={capturedImage} alt="撮影した商品" style={styles.cameraVideo} />
          </div>
        )}

        {cameraError && <div style={styles.cameraErrorText}>{cameraError}</div>}

        <div style={styles.cameraBtnRow}>
          {!capturedImage && !cameraOn && (
            <button type="button" style={styles.primaryBtn} onClick={startCamera}>
              📷 カメラを起動する
            </button>
          )}
          {!capturedImage && cameraOn && (
            <button type="button" style={styles.primaryBtn} onClick={capture}>
              📸 撮影して照合する
            </button>
          )}
          {!capturedImage && (
            <label style={{ ...styles.secondaryBtn, textAlign: "center", cursor: "pointer" }}>
              写真を選んで照合
              <input type="file" accept="image/*" capture="environment" onChange={handleFileCapture} style={{ display: "none" }} />
            </label>
          )}
          {capturedImage && (
            <button type="button" style={styles.secondaryBtn} onClick={reset}>
              もう一度撮影する
            </button>
          )}
        </div>

        {(analyzing || modelLoading) && (
          <div style={styles.cameraLoadingText}>
            {modelLoading ? "画像認識モデルを準備中…（初回のみ少し時間がかかります）" : "照合中…"}
          </div>
        )}

        {matches && (
          <div style={styles.routeResultBox}>
            <div style={styles.routeResultTitle}>
              {matches.length === 0 ? "候補が見つかりませんでした" : "発注先の候補"}
            </div>
            {matches.map((m, idx) => {
              const supplier = supplierOf(m.supplierId);
              const percent = Math.max(0, Math.min(100, Math.round(m.score * 100)));
              return (
                <div key={m.id} style={styles.matchCard}>
                  <img src={m.image} alt={m.name} style={styles.matchThumb} />
                  <div style={{ flex: 1 }}>
                    <div style={styles.matchRank}>候補{idx + 1}・一致度 約{percent}%</div>
                    <div style={styles.matchName}>{m.name}</div>
                    <div style={styles.matchSupplier}>{m.supplierName}</div>
                    {supplier && (
                      <div style={styles.matchMeta}>
                        {supplier.phone} {supplier.contact ? `・担当: ${supplier.contact}` : ""}
                      </div>
                    )}
                  </div>
                </div>
              );
            })}
            <div style={styles.routeNote}>
              ※ 見た目の類似度による簡易判定です。同じ品目を複数業者から仕入れている場合、
              候補を確認のうえ最終判断してください。
            </div>
          </div>
        )}
      </div>
    </div>
  );
}

function RouteView({
  suppliers, routeStops, setRouteStops, routeStart, setRouteStart,
  routeStartCoord, setRouteStartCoord, calculateRoute, routeLoading,
  routeResult, totalDistance, mapRef,
}) {
  const toggleStop = (id) => {
    setRouteStops((prev) => prev.includes(id) ? prev.filter((x) => x !== id) : [...prev, id]);
  };

  return (
    <div style={styles.routeWrap}>
      <div style={styles.routePanel}>
        <div style={styles.routeSectionTitle}>出発地点</div>
        <div style={styles.routeStartBtnRow}>
          <button
            type="button"
            style={{
              ...styles.hqBtn,
              ...(routeStart === HQ_ADDRESS ? styles.hqBtnActive : {}),
            }}
            onClick={() => { setRouteStart(HQ_ADDRESS); setRouteStartCoord(null); }}
          >
            🏠 本社から出発
          </button>
        </div>
        <input
          style={styles.input}
          placeholder="出先から出発する場合は住所を入力（例：松本市〇〇）"
          value={routeStart}
          onChange={(e) => { setRouteStart(e.target.value); setRouteStartCoord(null); }}
        />
        <div style={styles.routeStartHint}>
          {routeStart === HQ_ADDRESS ? HQ_LABEL : "出先の住所が出発地点として使われます"}
        </div>

        <div style={{ ...styles.routeSectionTitle, marginTop: 16 }}>
          回りたい仕入先を選択（車移動）
        </div>
        <div style={styles.routeStopList}>
          {suppliers.map((s) => (
            <label key={s.id} style={styles.routeStopItem}>
              <input
                type="checkbox"
                checked={routeStops.includes(s.id)}
                onChange={() => toggleStop(s.id)}
              />
              <span
                style={{
                  ...styles.catChip,
                  background: categoryColor(s.category) + "22",
                  color: categoryColor(s.category),
                  marginRight: 6,
                }}
              >
                {categoryLabel(s.category)}
              </span>
              {s.name}
            </label>
          ))}
        </div>

        <button style={styles.primaryBtn} onClick={calculateRoute} disabled={routeLoading}>
          {routeLoading ? "計算中…" : "ルートを提案する"}
        </button>

        {routeResult && (
          <div style={styles.routeResultBox}>
            <div style={styles.routeResultTitle}>
              提案ルート（合計 約{totalDistance.toFixed(1)}km）
            </div>
            <ol style={styles.routeOrderedList}>
              {routeResult.map((r, idx) => (
                <li key={r.id} style={styles.routeOrderItem}>
                  <b>{r.name}</b>
                  <div style={styles.routeLegMeta}>
                    前の地点から約{r.legDistanceKm.toFixed(1)}km
                  </div>
                </li>
              ))}
            </ol>
            <div style={styles.routeNote}>
              ※ 直線距離をもとにした簡易的な順序提案です。実際の道路状況とは異なる場合があります。
            </div>
          </div>
        )}
      </div>
      <div style={styles.mapArea}>
        <div ref={mapRef} style={{ width: "100%", height: "100%" }} />
        {!routeResult && (
          <div style={styles.routeMapHint}>
            出発地点と仕入先を選んで「ルートを提案する」を押すと、ここに地図上のルートが表示されます。
          </div>
        )}
      </div>
    </div>
  );
}

function SupplierFormModal({ initial, onClose, onSave }) {
  const [form, setForm] = useState(
    initial || {
      name: "",
      category: "vegetable",
      address: "",
      phone: "",
      hours: "",
      closedDay: "",
      contact: "",
      website: "",
      notes: "",
      products: [],
      lat: null,
      lng: null,
    }
  );
  const [saving, setSaving] = useState(false);
  const [uploadingPhoto, setUploadingPhoto] = useState(false);

  const update = (key, val) => setForm((f) => ({ ...f, [key]: val }));

  const handlePhotoUpload = async (e) => {
    const file = e.target.files?.[0];
    if (!file) return;
    setUploadingPhoto(true);
    try {
      const dataUrl = await resizeImageFile(file, 800, 0.75);
      const name = window.prompt("この商品の名前を入力してください", file.name.replace(/\.[^.]+$/, "")) || file.name;
      let embedding = null;
      try {
        embedding = await embedFromDataUrl(dataUrl);
      } catch (embErr) {

        console.warn("特徴ベクトルの計算に失敗しました", embErr);
      }
      update("products", [...(form.products || []), { id: uid(), name, image: dataUrl, embedding }]);
    } catch (err) {
      window.alert("画像の読み込みに失敗しました");
    } finally {
      setUploadingPhoto(false);
      e.target.value = "";
    }
  };

  const removeProduct = (id) => {
    update("products", form.products.filter((p) => p.id !== id));
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!form.name.trim()) {
      window.alert("仕入先名を入力してください");
      return;
    }
    setSaving(true);
    await onSave(form);
    setSaving(false);
  };

  return (
    <div style={styles.modalOverlay} onClick={onClose}>
      <form style={styles.modal} onClick={(e) => e.stopPropagation()} onSubmit={handleSubmit}>
        <div style={styles.modalHeader}>
          <div style={styles.modalTitle}>{initial ? "仕入先を編集" : "仕入先を新規登録"}</div>
          <button type="button" style={styles.closeBtn} onClick={onClose}>×</button>
        </div>

        <div style={styles.modalBody}>
          <FieldRow label="仕入先名 *">
            <input style={styles.input} value={form.name} onChange={(e) => update("name", e.target.value)} required />
          </FieldRow>

          <FieldRow label="主な品目">
            <select style={styles.select} value={form.category} onChange={(e) => update("category", e.target.value)}>
              {CATEGORY_OPTIONS.map((c) => (
                <option key={c.key} value={c.key}>{c.label}</option>
              ))}
            </select>
          </FieldRow>

          <FieldRow label="住所" hint="保存時に自動で地図上の位置を取得します">
            <input style={styles.input} value={form.address} onChange={(e) => update("address", e.target.value)} placeholder="例：長野県大町市常盤7000" />
          </FieldRow>

          <div style={styles.fieldGrid2}>
            <FieldRow label="電話番号">
              <input style={styles.input} value={form.phone} onChange={(e) => update("phone", e.target.value)} placeholder="0261-22-0000" />
            </FieldRow>
            <FieldRow label="担当者名">
              <input style={styles.input} value={form.contact} onChange={(e) => update("contact", e.target.value)} placeholder="〇〇様" />
            </FieldRow>
          </div>

          <div style={styles.fieldGrid2}>
            <FieldRow label="営業時間">
              <input style={styles.input} value={form.hours} onChange={(e) => update("hours", e.target.value)} placeholder="9:00-18:00" />
            </FieldRow>
            <FieldRow label="定休日">
              <input style={styles.input} value={form.closedDay} onChange={(e) => update("closedDay", e.target.value)} placeholder="水曜" />
            </FieldRow>
          </div>

          <FieldRow label="ホームページURL">
            <input style={styles.input} value={form.website} onChange={(e) => update("website", e.target.value)} placeholder="https://..." />
          </FieldRow>

          <FieldRow label="メモ">
            <textarea style={{ ...styles.input, minHeight: 60, resize: "vertical" }} value={form.notes} onChange={(e) => update("notes", e.target.value)} />
          </FieldRow>

          <FieldRow label="商品写真" hint="倉庫で在庫切れの際に見た目を確認できます">
            <label style={styles.uploadBtn}>
              {uploadingPhoto ? "解析中…" : "＋ 写真を追加"}
              <input type="file" accept="image/*" onChange={handlePhotoUpload} style={{ display: "none" }} />
            </label>
            <div style={styles.productThumbRow}>
              {(form.products || []).map((p) => (
                <div key={p.id} style={styles.productThumb}>
                  <img src={p.image} alt={p.name} style={styles.productThumbImg} />
                  <div style={styles.productThumbName}>{p.name}</div>
                  <button type="button" style={styles.productThumbRemove} onClick={() => removeProduct(p.id)}>×</button>
                </div>
              ))}
            </div>
          </FieldRow>
        </div>

        <div style={styles.modalFooter}>
          <button type="button" style={styles.secondaryBtn} onClick={onClose}>キャンセル</button>
          <button type="submit" style={styles.primaryBtn} disabled={saving}>
            {saving ? "保存中…" : "保存する"}
          </button>
        </div>
      </form>
    </div>
  );
}

function FieldRow({ label, hint, children }) {
  return (
    <div style={styles.fieldRow}>
      <div style={styles.fieldLabel}>
        {label}
        {hint && <span style={styles.fieldHint}>{hint}</span>}
      </div>
      {children}
    </div>
  );
}

const styles = {
  app: {
    width: "100%",
    height: "100vh",
    minHeight: 600,
    display: "flex",
    flexDirection: "column",
    background: COLORS.bg,
    fontFamily: "'Hiragino Sans', 'Noto Sans JP', sans-serif",
    color: COLORS.ink,
  },
  header: {
    display: "flex",
    alignItems: "center",
    gap: 16,
    padding: "12px 20px",
    borderBottom: `1px solid ${COLORS.line}`,
    background: COLORS.card,
    flexWrap: "wrap",
  },
  brand: { display: "flex", alignItems: "center", gap: 10, marginRight: 8 },
  brandMark: {
    width: 38, height: 38, borderRadius: 9,
    background: COLORS.ink, color: COLORS.bg,
    display: "flex", alignItems: "center", justifyContent: "center",
    fontWeight: 700, fontSize: 18,
    fontFamily: "serif",
  },
  brandTitle: { fontWeight: 700, fontSize: 15, lineHeight: 1.2 },
  brandSub: { fontSize: 11, color: COLORS.inkSoft },
  tabs: { display: "flex", gap: 4, flex: 1 },
  tabBtn: {
    padding: "8px 14px", border: "none", background: "transparent",
    borderRadius: 8, cursor: "pointer", fontSize: 13.5, color: COLORS.inkSoft,
    fontWeight: 600,
  },
  tabBtnActive: { background: COLORS.accentSoft, color: COLORS.ink },
  addBtn: {
    padding: "9px 16px", borderRadius: 8, border: "none",
    background: COLORS.accent, color: "#fff", fontWeight: 700,
    fontSize: 13.5, cursor: "pointer",
  },
  body: { flex: 1, display: "flex", minHeight: 0 },
  sidebar: {
    width: 320, borderRight: `1px solid ${COLORS.line}`,
    display: "flex", flexDirection: "column", background: COLORS.card,
  },
  filterRow: {
    padding: 12, borderBottom: `1px solid ${COLORS.line}`,
    display: "flex", gap: 10, alignItems: "center", flexWrap: "wrap",
  },
  select: {
    padding: "8px 10px", borderRadius: 8, border: `1px solid ${COLORS.line}`,
    fontSize: 13, background: "#fff", color: COLORS.ink,
  },
  sidebarList: { flex: 1, overflowY: "auto", padding: 10 },
  emptyState: { padding: 20, color: COLORS.inkSoft, fontSize: 13, lineHeight: 1.6 },
  supplierCard: {
    border: `1px solid ${COLORS.line}`, borderRadius: 10, padding: 12,
    marginBottom: 10, cursor: "pointer", transition: "border 0.15s",
  },
  supplierCardActive: { border: `1.5px solid ${COLORS.accent}` },
  cardTopRow: { marginBottom: 6 },
  catChip: { fontSize: 11, fontWeight: 700, padding: "3px 8px", borderRadius: 6 },
  cardName: { fontWeight: 700, fontSize: 14.5, marginBottom: 4 },
  cardMeta: { fontSize: 12, color: COLORS.inkSoft, marginBottom: 2 },
  cardActions: { display: "flex", gap: 10, marginTop: 8, alignItems: "center" },
  cardLink: { fontSize: 12, color: COLORS.accent, fontWeight: 700, textDecoration: "none" },
  cardSmallBtn: {
    fontSize: 12, background: "transparent", border: "none", cursor: "pointer",
    color: COLORS.inkSoft, fontWeight: 600, padding: 0,
  },
  mapArea: { flex: 1, position: "relative" },
  listWrap: { flex: 1, overflow: "auto", padding: 20 },
  table: { width: "100%", borderCollapse: "collapse", background: COLORS.card, fontSize: 13 },
  th: {
    textAlign: "left", padding: "10px 12px", borderBottom: `2px solid ${COLORS.line}`,
    color: COLORS.inkSoft, fontWeight: 700, fontSize: 12,
  },
  tr: { borderBottom: `1px solid ${COLORS.line}` },
  td: { padding: "10px 12px", verticalAlign: "top" },
  galleryWrap: { flex: 1, overflow: "auto", padding: 20 },
  photoGrid: {
    display: "grid", gridTemplateColumns: "repeat(auto-fill, minmax(160px, 1fr))", gap: 14,
  },
  photoCard: {
    border: `1px solid ${COLORS.line}`, borderRadius: 10, overflow: "hidden", background: COLORS.card,
  },
  photoImg: { width: "100%", height: 130, objectFit: "cover", display: "block" },
  photoCaption: { padding: 10 },
  photoName: { fontWeight: 700, fontSize: 13 },
  photoSupplier: { fontSize: 11, color: COLORS.inkSoft, marginTop: 2 },
  routeWrap: { flex: 1, display: "flex", minHeight: 0 },
  routePanel: {
    width: 340, borderRight: `1px solid ${COLORS.line}`, background: COLORS.card,
    padding: 16, overflowY: "auto",
  },
  routeSectionTitle: { fontWeight: 700, fontSize: 13, marginBottom: 8, color: COLORS.ink },
  routeStartBtnRow: { marginBottom: 10 },
  hqBtn: {
    padding: "9px 14px", borderRadius: 8, border: `1px solid ${COLORS.accent}`,
    background: "#fff", color: COLORS.accent, fontWeight: 700, fontSize: 13,
    cursor: "pointer", width: "100%",
  },
  hqBtnActive: { background: COLORS.accent, color: "#fff" },
  routeStartHint: { fontSize: 11.5, color: COLORS.inkSoft, marginTop: 6, marginBottom: 4 },
  routeStopList: { display: "flex", flexDirection: "column", gap: 6, maxHeight: 260, overflowY: "auto" },
  routeStopItem: { display: "flex", alignItems: "center", gap: 8, fontSize: 13, cursor: "pointer" },
  routeResultBox: { marginTop: 18, borderTop: `1px solid ${COLORS.line}`, paddingTop: 14 },
  routeResultTitle: { fontWeight: 700, fontSize: 13, marginBottom: 8 },
  routeOrderedList: { paddingLeft: 18, margin: 0, fontSize: 13 },
  routeOrderItem: { marginBottom: 10 },
  routeLegMeta: { fontSize: 11.5, color: COLORS.inkSoft },
  routeNote: { fontSize: 11, color: COLORS.inkSoft, marginTop: 8, lineHeight: 1.5 },
  routeMapHint: {
    position: "absolute", top: "50%", left: "50%", transform: "translate(-50%,-50%)",
    background: "rgba(255,255,255,0.92)", padding: "14px 20px", borderRadius: 10,
    fontSize: 13, color: COLORS.inkSoft, maxWidth: 280, textAlign: "center",
    boxShadow: "0 4px 16px rgba(0,0,0,0.1)",
  },
  cameraWrap: { flex: 1, display: "flex", justifyContent: "center", overflow: "auto", padding: 20 },
  cameraPanel: {
    width: "100%", maxWidth: 480, background: COLORS.card, borderRadius: 14,
    border: `1px solid ${COLORS.line}`, padding: 20, height: "fit-content",
  },
  cameraDesc: { fontSize: 12.5, color: COLORS.inkSoft, lineHeight: 1.6, marginBottom: 14 },
  cameraNotice: {
    fontSize: 12.5, color: COLORS.ink, background: COLORS.accentSoft + "66",
    border: `1px solid ${COLORS.accentSoft}`, borderRadius: 8, padding: 10, marginBottom: 14, lineHeight: 1.6,
  },
  cameraStage: {
    width: "100%", aspectRatio: "4 / 3", borderRadius: 12, overflow: "hidden",
    background: "#0000000d", display: "flex", alignItems: "center", justifyContent: "center",
    marginBottom: 12, border: `1px solid ${COLORS.line}`,
  },
  cameraVideo: { width: "100%", height: "100%", objectFit: "cover", display: "block" },
  cameraPlaceholder: { color: COLORS.inkSoft, fontSize: 13, textAlign: "center", padding: 20 },
  cameraErrorText: { color: COLORS.danger, fontSize: 12.5, marginBottom: 10, lineHeight: 1.5 },
  cameraBtnRow: { display: "flex", flexDirection: "column", gap: 8 },
  cameraLoadingText: { fontSize: 12.5, color: COLORS.inkSoft, marginTop: 10, textAlign: "center" },
  matchCard: {
    display: "flex", gap: 12, alignItems: "center", padding: "10px 0",
    borderBottom: `1px solid ${COLORS.line}`,
  },
  matchThumb: { width: 56, height: 56, objectFit: "cover", borderRadius: 8, border: `1px solid ${COLORS.line}`, flexShrink: 0 },
  matchRank: { fontSize: 11, color: COLORS.accent, fontWeight: 700, marginBottom: 2 },
  matchName: { fontWeight: 700, fontSize: 13.5 },
  matchSupplier: { fontSize: 12, color: COLORS.inkSoft, marginTop: 1 },
  matchMeta: { fontSize: 11, color: COLORS.inkSoft, marginTop: 2 },
  input: {
    width: "100%", padding: "9px 11px", borderRadius: 8, border: `1px solid ${COLORS.line}`,
    fontSize: 13.5, background: "#fff", color: COLORS.ink, fontFamily: "inherit",
  },
  primaryBtn: {
    width: "100%", marginTop: 14, padding: "11px", borderRadius: 8, border: "none",
    background: COLORS.accent, color: "#fff", fontWeight: 700, fontSize: 14, cursor: "pointer",
  },
  secondaryBtn: {
    padding: "10px 18px", borderRadius: 8, border: `1px solid ${COLORS.line}`,
    background: "#fff", color: COLORS.ink, fontWeight: 600, fontSize: 13.5, cursor: "pointer",
  },
  modalOverlay: {
    position: "fixed", inset: 0, background: "rgba(31,46,38,0.45)",
    display: "flex", alignItems: "center", justifyContent: "center", zIndex: 50, padding: 20,
  },
  modal: {
    background: COLORS.bg, borderRadius: 14, width: "100%", maxWidth: 520,
    maxHeight: "88vh", display: "flex", flexDirection: "column", overflow: "hidden",
    boxShadow: "0 20px 60px rgba(0,0,0,0.25)",
  },
  modalHeader: {
    display: "flex", justifyContent: "space-between", alignItems: "center",
    padding: "16px 20px", borderBottom: `1px solid ${COLORS.line}`, background: COLORS.card,
  },
  modalTitle: { fontWeight: 700, fontSize: 16 },
  closeBtn: { border: "none", background: "transparent", fontSize: 20, cursor: "pointer", color: COLORS.inkSoft },
  modalBody: { padding: 20, overflowY: "auto", flex: 1 },
  modalFooter: {
    display: "flex", justifyContent: "flex-end", gap: 10, padding: 16,
    borderTop: `1px solid ${COLORS.line}`, background: COLORS.card,
  },
  fieldRow: { marginBottom: 14 },
  fieldLabel: { fontSize: 12.5, fontWeight: 700, marginBottom: 6, color: COLORS.ink, display: "flex", gap: 8, alignItems: "baseline" },
  fieldHint: { fontSize: 11, fontWeight: 400, color: COLORS.inkSoft },
  fieldGrid2: { display: "grid", gridTemplateColumns: "1fr 1fr", gap: 12 },
  uploadBtn: {
    display: "inline-block", padding: "8px 14px", borderRadius: 8,
    border: `1px dashed ${COLORS.accent}`, color: COLORS.accent, fontSize: 13, fontWeight: 700,
    cursor: "pointer", background: COLORS.accentSoft + "55",
  },
  productThumbRow: { display: "flex", gap: 10, flexWrap: "wrap", marginTop: 10 },
  productThumb: { position: "relative", width: 80 },
  productThumbImg: { width: 80, height: 80, objectFit: "cover", borderRadius: 8, display: "block", border: `1px solid ${COLORS.line}` },
  productThumbName: { fontSize: 10.5, marginTop: 4, textAlign: "center", color: COLORS.inkSoft, wordBreak: "break-word" },
  productThumbRemove: {
    position: "absolute", top: -6, right: -6, width: 20, height: 20, borderRadius: "50%",
    border: "none", background: COLORS.danger, color: "#fff", fontSize: 12, cursor: "pointer",
    display: "flex", alignItems: "center", justifyContent: "center",
  },
  toast: {
    position: "fixed", bottom: 24, left: "50%", transform: "translateX(-50%)",
    background: COLORS.ink, color: "#fff", padding: "10px 18px", borderRadius: 10,
    fontSize: 13, boxShadow: "0 8px 24px rgba(0,0,0,0.25)", zIndex: 100,
  },
  footer: {
    textAlign: "center", padding: "8px 0", fontSize: 11, color: COLORS.inkSoft,
    borderTop: `1px solid ${COLORS.line}`, background: COLORS.card,
  },
};
