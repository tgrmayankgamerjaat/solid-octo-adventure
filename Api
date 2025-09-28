const ADMIN_KEY = process.env.ADMIN_KEY || "MRWEIRDO";
const keys = new Set(["DEMOKEY"]); // demo key for testing

// Build target URLs
function buildTargetUrl(type, term) {
  if (!term) return null;
  type = type?.toLowerCase();
  if (type === "mobile") return `https://osintx.info/API/aetherdemo.php?key=SANJ33T&type=mobile&term=${encodeURIComponent(term)}`;
  if (type === "id_number") return `https://osintx.info/API/aetherdemo.php?key=SANJ33T&type=id_number&term=${encodeURIComponent(term)}`;
  if (type === "rc") return `https://osintx.info/API/new1vehicle.php?key=SANJ33T&rc=${encodeURIComponent(term)}`;
  if (type === "cnic") return `https://osintx.info/API/pak.php?key=TEMPORARY&cnic=${encodeURIComponent(term)}`;
  if (type === "upi_id") return `https://osintx.info/API/uppi.php?key=SANJ33T&upi_id=${encodeURIComponent(term)}`;
  if (type === "number") return `https://pak-num-api.vercel.app/search?number=${encodeURIComponent(term)}`;
  if (type === "ifsc") return `https://ifsc.razorpay.com/${encodeURIComponent(term)}`;
  return null;
}

async function proxyFetch(url) {
  const r = await fetch(url);
  const ct = r.headers.get("content-type") || "";
  const body = await r.text();
  try {
    if (ct.includes("application/json") || body.trim().startsWith("{")) {
      return { json: JSON.parse(body) };
    }
  } catch (e) {}
  return { text: body };
}

export default async function handler(req, res) {
  const q = req.query || {};

  // Admin endpoints
  if (req.url.includes("/api/key")) {
    const provided = q.key || "";
    if (provided !== ADMIN_KEY) {
      return res.status(401).json({ success: false, error: "invalid admin key" });
    }

    // Add custom key
    if (q.genkey) {
      const newKey = q.genkey.trim();
      keys.add(newKey);
      return res.json({ success: true, added: newKey, note: "IN-MEMORY only (prototype)" });
    }

    // Delete key
    if (q.delkey) {
      const deleted = keys.delete(q.delkey.trim());
      return res.json({ success: true, deleted });
    }

    // List keys
    if ("keylist" in q) {
      return res.json({ success: true, keys: Array.from(keys) });
    }

    return res.json({ success: false, error: "No admin action specified" });
  }

  // User API
  const userKey = q.key;
  if (!userKey) return res.status(400).json({ success: false, error: "missing key" });
  if (!keys.has(userKey)) return res.status(403).json({ success: false, error: "invalid key" });

  const type = q.type;
  const term = q.term;
  if (!type || !term) return res.status(400).json({ success: false, error: "missing type or term" });

  const target = buildTargetUrl(type, term);
  if (!target) return res.status(400).json({ success: false, error: "unknown type" });

  try {
    const prox = await proxyFetch(target);
    return res.json({ success: true, result: prox.json || prox.text });
  } catch (e) {
    return res.status(500).json({ success: false, error: e.toString() });
  }
}
