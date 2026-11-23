---
name: drawio-architecture-diagram
description: Vẽ flow diagram kiến trúc dạng Swimlane Architecture Flow bằng draw.io XML. Dùng khi cần tạo diagram tổng quan kiến trúc hệ thống, SDK, hoặc ứng dụng.
---

# Draw.io Swimlane Architecture Flow Diagram

## Khi nào sử dụng skill này

- Khi user yêu cầu vẽ **diagram kiến trúc**, **flow diagram**, hoặc **architecture overview**
- Khi user nói: "vẽ diagram", "vẽ flow", "tạo sơ đồ kiến trúc", "draw.io diagram"
- Khi user tham chiếu đến style "Swimlane Architecture Flow" hoặc file `Diagram_CShield.drawio.xml`

## Format bắt buộc

- **PHẢI dùng draw.io XML format** (`mxGraphModel`) — gọi tool `mcp_drawio_open_drawio_xml`
- **KHÔNG dùng Mermaid** — Mermaid không hỗ trợ đủ layout và style chi tiết
- **KHÔNG dùng draw.io CSV**

## Design Pattern: Swimlane Architecture Flow

### Layout tổng thể

```
Canvas: dx="3965" dy="1975", grid="1", gridSize="10", shadow="0", page="0"
Mỗi swimlane zone: width ~1640px, nằm ngang
Các zone xếp dọc từ trên xuống dưới
```

### Cấu trúc chính

```
1. TITLE + SUBTITLE — text node trên cùng, căn giữa
2. SWIMLANE ZONES   — các zone xếp dọc (Build Time, Runtime, Detection, ...)
3. NODES            — rectangle sắc cạnh bên trong zone, màu đậm, text trắng
4. EDGES            — mũi tên có màu theo loại (xanh lá=normal, đỏ=fail, cam=external)
5. NOTES            — inline text gắn cạnh node/edge (không border)
6. LEGEND           — zone chú thích cuối cùng
```

### Cấu trúc zones (từ trên xuống)

```
1. TITLE        — text node, fontSize=24, fontStyle=1
2. BUILD TIME   — swimlane, fillColor=#e8f4f8, strokeColor=#2c7be5
3. RUNTIME      — swimlane, fillColor=#f0f9f0, strokeColor=#2d862d
4. DETECTION    — swimlane, fillColor=#f9f0ff, strokeColor=#7b00d4
5. WATCHDOG     — swimlane, fillColor=#fff0f0, strokeColor=#cc0000
6. AIP/API      — swimlane, fillColor=#fff8e6, strokeColor=#e6ac00
7. DETAIL zones — swimlane riêng cho từng detector (nếu cần)
8. LEGEND       — swimlane, fillColor=#f5f5f5, strokeColor=#888888
```

> **Lưu ý:** Thứ tự và số lượng zones linh hoạt tùy project. Trên đây là template gợi ý.

### Title + Subtitle template

```xml
<!-- Main title -->
<mxCell id="title" parent="1" vertex="1"
  style="text;html=1;align=center;verticalAlign=middle;fontStyle=1;fontSize=24;fontColor=#1a1a2e;"
  value="Tiêu đề Architecture Diagram">
  <mxGeometry x="300" y="10" width="600" height="35" as="geometry"/>
</mxCell>

<!-- Subtitle -->
<mxCell id="subtitle" parent="1" vertex="1"
  style="text;html=1;align=center;verticalAlign=middle;fontSize=12;fontColor=#888888;fontStyle=2;"
  value="Module Name — Architecture Overview">
  <mxGeometry x="300" y="40" width="600" height="20" as="geometry"/>
</mxCell>
```

### Swimlane zone template

```xml
<mxCell id="zone_id" parent="1"
  style="swimlane;startSize=30;fillColor=#e8f4f8;strokeColor=#2c7be5;fontStyle=1;fontSize=13;fontColor=#1a3a5c;"
  value="ZONE TITLE" vertex="1">
  <mxGeometry height="400" width="1640" x="-22" y="80" as="geometry"/>
</mxCell>
```

### Node template (Rectangle sắc cạnh, màu đậm)

```xml
<mxCell id="node_id" parent="zone_id"
  style="rounded=0;whiteSpace=wrap;html=1;fillColor=#5b9bd5;strokeColor=#4a8ac4;fontColor=#ffffff;fontStyle=1;fontSize=10;"
  value="Title Line&#10;Detail line 1&#10;Detail line 2" vertex="1">
  <mxGeometry height="70" width="200" x="20" y="50" as="geometry"/>
</mxCell>
```

- **rounded=0**: Cạnh vuông, KHÔNG bo tròn
- Dùng `&#10;` cho xuống dòng trong value
- **fontColor=#ffffff**: Text trắng trên nền đậm
- `fontStyle=1` cho bold
- `fontSize=10-11` cho nội dung, `fontSize=13` cho zone title

### Edge template (CÓ MÀU theo loại)

```xml
<!-- Normal edge (màu xanh lá) -->
<mxCell id="edge_id" edge="1" parent="zone_id" source="src_id"
  style="edgeStyle=orthogonalEdgeStyle;strokeColor=#2d862d;strokeWidth=1.5;endArrow=block;endFill=1;fontSize=10;fontColor=#2d862d;"
  target="tgt_id">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>

<!-- FAIL edge (màu đỏ) -->
<mxCell id="fail_edge" edge="1" parent="zone_id" source="src_id"
  style="edgeStyle=orthogonalEdgeStyle;strokeColor=#cc0000;strokeWidth=1.5;endArrow=block;endFill=1;fontColor=#cc0000;fontStyle=1;fontSize=10;"
  target="kill_node" value="FAIL">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>

<!-- OK edge (màu xanh lá) -->
<mxCell id="ok_edge" edge="1" parent="zone_id" source="src_id"
  style="edgeStyle=orthogonalEdgeStyle;strokeColor=#2d862d;strokeWidth=1.5;endArrow=block;endFill=1;fontColor=#2d862d;fontStyle=1;fontSize=10;"
  target="sleep_node" value="OK">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>

<!-- External / outgoing edge (màu cam) -->
<mxCell id="ext_edge" edge="1" parent="zone_id" source="src_id"
  style="edgeStyle=orthogonalEdgeStyle;strokeColor=#e6a800;strokeWidth=1.5;endArrow=block;endFill=1;fontColor=#e6a800;fontStyle=1;fontSize=10;"
  target="ext_node" value="API Call">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>

<!-- Return / Response edge (dashed, xanh lá) -->
<mxCell id="ret_edge" edge="1" parent="zone_id" source="src_id"
  style="edgeStyle=orthogonalEdgeStyle;dashed=1;dashPattern=8 4;strokeColor=#2d862d;strokeWidth=1.5;endArrow=open;endFill=0;fontColor=#2d862d;fontSize=10;"
  target="tgt_id" value="response">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>

<!-- Callback / Event edge (màu đỏ cam) -->
<mxCell id="cb_edge" edge="1" parent="zone_id" source="src_id"
  style="edgeStyle=orthogonalEdgeStyle;strokeColor=#e64a19;strokeWidth=1.5;endArrow=block;endFill=1;fontColor=#e64a19;fontStyle=1;fontSize=10;"
  target="tgt_id" value="onEvent()">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>
```

> **Quy tắc:** `strokeColor` và `fontColor` của edge PHẢI cùng màu. `labelBackgroundColor=none` khi cần.

### Edge với waypoints (khi cần điều hướng phức tạp)

```xml
<mxCell id="edge_wp" edge="1" parent="zone_id" source="src" target="tgt"
  style="edgeStyle=orthogonalEdgeStyle;strokeColor=#2d862d;strokeWidth=1.5;endArrow=block;endFill=1;">
  <mxGeometry relative="1" as="geometry">
    <Array as="points">
      <mxPoint x="500" y="200"/>
      <mxPoint x="500" y="400"/>
    </Array>
  </mxGeometry>
</mxCell>
```

### Note template (inline text, không border)

```xml
<!-- Note nhỏ gắn cạnh node/edge, không có box border -->
<mxCell id="note1" parent="zone_id" vertex="1"
  style="text;html=1;align=left;verticalAlign=middle;fontSize=10;fontColor=#888888;fontStyle=2;fillColor=none;strokeColor=none;"
  value="Mô tả ngắn gọn logic">
  <mxGeometry x="230" y="55" width="180" height="20" as="geometry"/>
</mxCell>
```

- **Không có border** (`strokeColor=none;fillColor=none`)
- **fontStyle=2** (italic), `fontColor=#888888` (xám)

## Bảng màu Node (Color Scheme)

| Vai trò | fillColor | strokeColor | fontColor | Khi nào dùng |
|---------|-----------|-------------|-----------|--------------|
| Core / integrity check | `#5b9bd5` | `#4a8ac4` | `#ffffff` | Main flow, hash verify, prologue check |
| Init pipeline / load | `#4caf50` | `#43a047` | `#ffffff` | Loading steps, init sequence |
| Build-time asset / key | `#ff9800` | `#e68a00` | `#ffffff` | CCS files, keys, configs |
| Anti-tamper / integrity | `#e6a800` | `#cc9600` | `#ffffff` | Constructor, mprotect, anti-debug |
| Detection / RASP | `#e91e63` | `#c2185b` | `#ffffff` | Frida, hooking, jailbreak, root checks |
| Config / device state | `#7b68ee` | `#6a5acd` | `#ffffff` | Settings, GOT snapshot, utilities |
| Central orchestrator | `#1a3a5c` | `#0f2e4d` | `#ffffff` | Main checker |
| Kill / terminate | `#990000` | `#660000` | `#ffffff` | KILL actions |
| Sleep / wait | `#666666` | `#444444` | `#ffffff` | Sleep states |
| Swift / public API | `#00bcd4` | `#00a5bb` | `#ffffff` | Public-facing API layer |

> **Lưu ý:** Tất cả node dùng **fontColor trắng** (`#ffffff`) trên nền đậm. `rounded=0` (sắc cạnh).

## Bảng màu Edge (Color Scheme)

| Loại Edge | strokeColor | fontColor | Style | Mô tả |
|-----------|-------------|-----------|-------|-------|
| Normal flow | `#2d862d` | `#2d862d` | `endArrow=block;endFill=1;` | Luồng chính |
| OK / Success | `#2d862d` | `#2d862d` | `endArrow=block;endFill=1;fontStyle=1;` | Kết quả thành công |
| FAIL / Error | `#cc0000` | `#cc0000` | `endArrow=block;endFill=1;fontStyle=1;` | Lỗi, fail |
| External call | `#e6a800` | `#e6a800` | `endArrow=block;endFill=1;fontStyle=1;` | Gọi ra bên ngoài |
| Return / Response | `#2d862d` | `#2d862d` | `dashed=1;endArrow=open;endFill=0;` | Response trả về |
| Callback / Event | `#e64a19` | `#e64a19` | `endArrow=block;endFill=1;fontStyle=1;` | Callback, event |

## Các loại node đặc biệt

### Kill node (terminate)
```xml
<mxCell style="rounded=0;whiteSpace=wrap;html=1;fillColor=#990000;strokeColor=#660000;fontColor=#ffffff;fontStyle=1;fontSize=10;"
  value="KILL_INLINE()&#10;Description" vertex="1">
```

### Cycle node (watchdog loop start)
```xml
<mxCell style="ellipse;whiteSpace=wrap;html=1;fillColor=#cc0000;strokeColor=#990000;fontColor=#ffffff;fontStyle=1;fontSize=11;"
  value="New Cycle" vertex="1">
```

### Central orchestrator node
```xml
<mxCell style="rounded=0;whiteSpace=wrap;html=1;fillColor=#1a3a5c;strokeColor=#0f2e4d;fontColor=#ffffff;fontStyle=1;fontSize=10;"
  value="Main Checker&#10;Details" vertex="1">
```

### Sub-swimlane (package/group bên trong zone)
```xml
<mxCell parent="parent_zone"
  style="swimlane;startSize=25;fillColor=#f5f5f5;strokeColor=#666666;fontStyle=1;fontSize=12;fontColor=#333333;"
  value="Sub-group Title" vertex="1">
```

## Quy trình thực hiện

### Bước 1: Review code
- Đọc toàn bộ source code của project/module cần vẽ
- Xác định các layer, component, flow chính
- Ghi nhận các detection/check functions, init sequence, kill mechanisms

### Bước 2: Phân chia zones
- Nhóm các component thành các swimlane zones
- Thường có: Build Time → Runtime Init → Detection → Watchdog → API → Legend
- Linh hoạt thêm/bớt zone tùy project

### Bước 3: Vẽ diagram
- Tạo XML theo template trên
- Thứ tự render trong XML:
  1. Title + Subtitle
  2. Swimlane zones
  3. Nodes bên trong zones
  4. Edges
  5. Notes
  6. Legend zone
- Gọi `mcp_drawio_open_drawio_xml` với content là XML
- **KHÔNG set `dark` parameter** — để auto

### Bước 4: Save file (nếu user yêu cầu)
- Lưu XML ra file `.drawio.xml` trong thư mục `docs/` của project

## Lưu ý quan trọng

1. **Mỗi mxCell PHẢI có unique id** — không trùng nhau
2. **Child nodes phải có `parent="zone_id"`** — để nằm trong đúng swimlane
3. **Edge cells phải có `<mxGeometry relative="1" as="geometry"/>`** — bắt buộc
4. **Dùng `&#10;` cho newline** trong value, KHÔNG dùng `\n`
5. **Dùng `&amp;` thay `&`**, `&lt;` thay `<`, `&gt;` thay `>` trong value attribute
6. **Nodes dùng `rounded=0`** (sắc cạnh), **fontColor=#ffffff** (text trắng trên nền đậm)
7. **Edge colors PHẢI nhất quán**: `strokeColor` = `fontColor` cùng màu
8. **Notes: inline text** (`fillColor=none;strokeColor=none`), `fontStyle=2` (italic), `fontColor=#888888`
9. **Width mỗi zone ~1640px** để đồng nhất
10. **fontSize: title=24, subtitle=12, zone title=13, node=10-11, edge label=10, note=10**
11. **Tránh XML comment dùng `--`** — draw.io không parse được

## Ví dụ câu lệnh từ user

```
"Vẽ flow diagram.io theo style Swimlane Architecture Flow"
"Review code rồi vẽ diagram kiến trúc bằng draw.io"
"Tạo architecture diagram cho project này, dùng draw.io XML"
"Vẽ diagram theo mẫu Diagram_CShield.drawio.xml"
```
