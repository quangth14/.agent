---
name: drawio-sequence-diagram
description: Vẽ Sequence Diagram (biểu đồ tuần tự) bằng draw.io XML. Dùng khi cần mô tả luồng tương tác giữa các actor/component theo thứ tự thời gian.
---

# Draw.io Sequence Diagram

## Khi nào sử dụng skill này

- Khi user yêu cầu vẽ **sequence diagram**, **biểu đồ tuần tự**, hoặc **interaction diagram**
- Khi user nói: "vẽ sequence diagram", "vẽ biểu đồ tuần tự", "vẽ luồng tương tác", "tạo sequence draw.io"
- Khi cần mô tả **thứ tự gọi hàm**, **luồng request/response**, hoặc **tương tác giữa các component theo thời gian**

## Format bắt buộc

- **PHẢI dùng draw.io XML format** (`mxGraphModel`) — gọi tool `mcp_drawio_open_drawio_xml`
- **KHÔNG dùng Mermaid** — Mermaid không hỗ trợ đủ layout và style chi tiết
- **KHÔNG dùng draw.io CSV**

## Design Pattern: Sequence Diagram

### Layout tổng thể

```
Canvas: dx="2800" dy="2000", grid="1", gridSize="10", shadow="0", page="0"
Participants xếp ngang ở top, cách đều nhau ~180-210px
Lifeline dọc xuống dưới mỗi participant (edge, CÓ MÀU theo participant)
Step numbers cột bên trái (x=20), mỗi step cách nhau ~50px theo chiều dọc
Horizontal guide lines mờ (strokeColor=#e0e0e0) nối ngang để dễ đọc step
Messages nối giữa các lifeline theo thứ tự thời gian từ trên xuống
KHÔNG dùng activation box — giữ diagram sạch và dễ đọc
```

### Cấu trúc chính

```
1. TITLE + SUBTITLE — text node trên cùng, căn giữa
2. STEP NUMBERS     — cột số thứ tự bên trái (1, 2, 3, ...)
3. HORIZONTAL LINES — đường kẻ ngang mờ cho mỗi step
4. PARTICIPANTS     — rectangle sắc cạnh, màu đậm, text trắng, 2 dòng (tên + vai trò)
5. LIFELINES        — edge dashed dọc, CÓ MÀU THEO PARTICIPANT (strokeColor = participant strokeColor)
6. MESSAGES         — mũi tên có màu, dùng sourcePoint/targetPoint trực tiếp trên trục lifeline
7. SELF-CALLS       — mũi tên vuông góc quay lại chính mình
8. NOTES            — text label nhỏ gắn cạnh message (không có box border)
9. FRAGMENTS        — package shape với label (alt, loop, opt, break)
```

### Title + Subtitle template

```xml
<!-- Main title -->
<mxCell id="title" parent="1" vertex="1"
  style="text;html=1;align=center;verticalAlign=middle;fontStyle=1;fontSize=24;fontColor=#1a1a2e;"
  value="Tiêu đề Diagram">
  <mxGeometry x="300" y="10" width="600" height="35" as="geometry"/>
</mxCell>

<!-- Subtitle -->
<mxCell id="subtitle" parent="1" vertex="1"
  style="text;html=1;align=center;verticalAlign=middle;fontSize=11;fontColor=#888888;fontStyle=2;"
  value="Module Name — Use Case Description">
  <mxGeometry x="300" y="40" width="600" height="20" as="geometry"/>
</mxCell>
```

### Participant template (Rectangle sắc cạnh, màu đậm)

```xml
<!-- Participant: 2 dòng (Tên + Vai trò), rounded=0 (sắc cạnh) -->
<mxCell id="p1" parent="1" vertex="1"
  style="rounded=0;whiteSpace=wrap;html=1;fillColor=#4a90d9;strokeColor=#3a7bc8;fontStyle=1;fontSize=11;fontColor=#ffffff;verticalAlign=middle;"
  value="ComponentName&#10;(Role)">
  <mxGeometry x="100" y="70" width="140" height="50" as="geometry"/>
</mxCell>
```

- **rounded=0**: Cạnh vuông, KHÔNG bo tròn
- **2 dòng text**: Dòng 1 = tên component, Dòng 2 = vai trò trong ngoặc `(Role)`
- **fontColor=#ffffff**: Text trắng trên nền đậm
- **fontSize=11**: Nhỏ hơn một chút để vừa 2 dòng
- **Height=50px**: Cao hơn bình thường để chứa 2 dòng

### Step Number + Horizontal Guide Line template

```xml
<!-- Step number bên trái -->
<mxCell id="step_1" parent="1" vertex="1"
  style="text;html=1;align=center;verticalAlign=middle;fontSize=12;fontColor=#4a90d9;fontStyle=1;"
  value="1">
  <mxGeometry x="20" y="140" width="30" height="20" as="geometry"/>
</mxCell>

<!-- Horizontal guide line mờ cho step -->
<mxCell id="hline_1" parent="1" vertex="1"
  style="line;strokeWidth=1;strokeColor=#e0e0e0;fillColor=none;"
  value="">
  <mxGeometry x="50" y="150" width="1300" height="1" as="geometry"/>
</mxCell>
```

- Step numbers: `x=20`, `width=30`, căn giữa
- Guide lines: `x=50` (sau cột số), kéo dài hết canvas (`width=1300`)
- Mỗi step cách nhau **50px** theo chiều dọc (y tăng 50 mỗi step)

### Lifeline template (edge dashed, CÓ MÀU THEO PARTICIPANT)

> **BẮT BUỘC:** Mỗi participant PHẢI có 1 lifeline dashed chạy **xuyên suốt từ đầu đến cuối** toàn bộ flow.
> **QUAN TRỌNG:** Lifeline dùng **`edge`** (không phải vertex), dùng **`dashPattern=12 12`**, và **strokeColor trùng với participant strokeColor** để dễ nhìn.

```xml
<!-- Lifeline: dùng EDGE, màu theo participant (strokeColor = participant strokeColor) -->
<mxCell id="ll1" edge="1" parent="1"
  style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;dashed=1;dashPattern=12 12;strokeColor=#3a7bc8;">
  <mxGeometry relative="1" as="geometry">
    <mxPoint x="169" y="120" as="sourcePoint"/>
    <mxPoint x="169" y="1750" as="targetPoint"/>
  </mxGeometry>
</mxCell>
```

- **`edge="1"`**: Dùng edge thay vì vertex
- **`dashPattern=12 12`**: Dash dài hơn so với `5 5` — tạo visual rõ ràng
- **`strokeColor` = participant `strokeColor`**: Mỗi lifeline có màu riêng theo participant
- `sourcePoint.x` = `targetPoint.x` = tâm participant (`participant_x + participant_width / 2`)
- `sourcePoint.y` = ngay dưới participant box (`participant_y + participant_height`)
- `targetPoint.y` = cuối flow (tất cả lifeline kết thúc cùng y)

#### Bảng màu lifeline:

| Participant | Lifeline strokeColor | Giải thích |
|-------------|---------------------|------------|
| Client `#5b9bd5` | `#5B9BD5` | Dùng fillColor hoặc strokeColor |
| Server `#1a3a5c` | `#1A3A5C` | |
| SDK `#00bcd4` | `#00BCD4` | |
| Validator `#e91e63` | `#C2185B` | Dùng strokeColor |
| Manager `#ff9800` | `#E68A00` | Dùng strokeColor |
| License Server `#7b68ee` | `#6A5ACD` | Dùng strokeColor |

### Message templates (KHÔNG dùng activation box, dùng sourcePoint/targetPoint)

> **QUAN TRỌNG:** KHÔNG dùng `source`/`target` attribute trỏ vào activation box. Thay vào đó dùng `sourcePoint`/`targetPoint` với tọa độ chính xác trên trục lifeline.

```xml
<!-- Synchronous call (mũi tên đặc, màu xanh lá) -->
<mxCell id="msg1" edge="1" parent="1"
  style="edgeStyle=none;strokeColor=#2d862d;strokeWidth=1.5;endArrow=block;endFill=1;fontSize=10;fontColor=#2d862d;fontStyle=1;labelBackgroundColor=none;align=center;"
  value="methodName()">
  <mxGeometry relative="1" as="geometry">
    <mxPoint y="-8" as="offset"/>
    <mxPoint x="356" y="225" as="sourcePoint"/>
    <mxPoint x="554" y="225" as="targetPoint"/>
  </mxGeometry>
</mxCell>

<!-- Return / Response (dashed, màu xanh lá) -->
<mxCell id="msg_ret" edge="1" parent="1"
  style="edgeStyle=none;dashed=1;dashPattern=8 4;strokeColor=#2d862d;strokeWidth=1.5;endArrow=open;endFill=0;fontSize=10;fontColor=#2d862d;labelBackgroundColor=none;"
  value="LicensePayload">
  <mxGeometry relative="1" as="geometry">
    <mxPoint y="-8" as="offset"/>
    <mxPoint x="554" y="340" as="sourcePoint"/>
    <mxPoint x="356" y="340" as="targetPoint"/>
  </mxGeometry>
</mxCell>

<!-- External / outgoing call (màu cam/vàng đậm) -->
<mxCell id="msg_ext" edge="1" parent="1"
  style="edgeStyle=none;strokeColor=#e6a800;strokeWidth=1.5;endArrow=block;endFill=1;fontSize=10;fontColor=#e6a800;fontStyle=1;labelBackgroundColor=none;"
  value="POST /license/validate">
  <mxGeometry relative="1" as="geometry">
    <mxPoint y="-8" as="offset"/>
    <mxPoint x="982" y="432" as="sourcePoint"/>
    <mxPoint x="1182" y="432" as="targetPoint"/>
  </mxGeometry>
</mxCell>

<!-- External return (dashed, màu cam) -->
<mxCell id="msg_ext_ret" edge="1" parent="1"
  style="edgeStyle=none;dashed=1;dashPattern=8 4;strokeColor=#e6a800;strokeWidth=1.5;endArrow=open;endFill=0;fontSize=10;fontColor=#e6a800;labelBackgroundColor=none;"
  value="{status, serverTime}">
  <mxGeometry relative="1" as="geometry">
    <mxPoint y="-8" as="offset"/>
    <mxPoint x="1182" y="473" as="sourcePoint"/>
    <mxPoint x="985" y="473" as="targetPoint"/>
  </mxGeometry>
</mxCell>

<!-- Error / Fail message (dashed, màu đỏ) -->
<mxCell id="msg_err" edge="1" parent="1"
  style="edgeStyle=none;dashed=1;dashPattern=8 4;strokeColor=#cc0000;strokeWidth=1.5;endArrow=open;endFill=0;fontSize=10;fontColor=#cc0000;fontStyle=1;labelBackgroundColor=none;"
  value="throw InvalidLicenseException">
  <mxGeometry relative="1" as="geometry">
    <mxPoint y="-8" as="offset"/>
    <mxPoint x="764" y="532" as="sourcePoint"/>
    <mxPoint x="356" y="532" as="targetPoint"/>
  </mxGeometry>
</mxCell>

<!-- Callback / Event (mũi tên đặc, màu đỏ cam) -->
<mxCell id="msg_callback" edge="1" parent="1"
  style="edgeStyle=none;strokeColor=#e64a19;strokeWidth=1.5;endArrow=block;endFill=1;fontSize=10;fontColor=#e64a19;fontStyle=1;labelBackgroundColor=none;"
  value="onStatusChanged(ACTIVE)">
  <mxGeometry relative="1" as="geometry">
    <mxPoint y="-8" as="offset"/>
    <mxPoint x="560" y="500" as="sourcePoint"/>
    <mxPoint x="150" y="500" as="targetPoint"/>
  </mxGeometry>
</mxCell>
```

#### Tọa độ sourcePoint/targetPoint:

- **sourcePoint.x** = tâm lifeline nguồn (vd: `participant_x + participant_width/2 + 7` lệch nhẹ khỏi tâm)
- **targetPoint.x** = tâm lifeline đích
- **sourcePoint.y** = **targetPoint.y** = cùng y (nằm trên cùng dòng ngang)

### Reverse arrow pattern

Khi cần vẽ mũi tên ngược chiều dùng `startArrow` thay `endArrow`:

```xml
<!-- Mũi tên chỉ từ phải sang trái nhưng source/target giữ nguyên thứ tự -->
<mxCell id="msg_rev" edge="1" parent="1"
  style="edgeStyle=none;strokeColor=#2d862d;strokeWidth=1.5;endArrow=none;endFill=0;startArrow=block;startFill=1;fontSize=10;fontColor=#2d862d;fontStyle=1;labelBackgroundColor=none;"
  value="validate(token)">
  <mxGeometry relative="1" as="geometry">
    <mxPoint y="-8" as="offset"/>
    <mxPoint x="776" y="249" as="sourcePoint"/>
    <mxPoint x="975" y="249" as="targetPoint"/>
  </mxGeometry>
</mxCell>
```

### Edge label as child cell

Khi cần label phức tạp (HTML), tạo label riêng là child cell:

```xml
<mxCell id="msg_complex" edge="1" parent="1"
  style="endArrow=block;html=1;rounded=0;endFill=1;strokeColor=#2D862D;fontStyle=1;strokeWidth=1.5;"
  value="">
  <mxGeometry height="50" relative="1" width="50" as="geometry">
    <mxPoint x="570" y="225" as="sourcePoint"/>
    <mxPoint x="976" y="224" as="targetPoint"/>
  </mxGeometry>
</mxCell>
<mxCell id="msg_complex_label" connectable="0" parent="msg_complex"
  style="edgeLabel;html=1;align=center;verticalAlign=middle;resizable=0;points=[];labelBackgroundColor=none;fontStyle=1;fontColor=#2D862D;"
  value="&lt;div&gt;&lt;span&gt;LicenseManager.init(config)&lt;/span&gt;&lt;/div&gt;" vertex="1">
  <mxGeometry relative="1" x="-0.13" y="3" as="geometry">
    <mxPoint x="33" y="-10" as="offset"/>
  </mxGeometry>
</mxCell>
```

### Self-call message (vuông góc)

```xml
<!-- Self-call: mũi tên vuông góc (phải -> xuống -> trái), dùng sourcePoint/targetPoint -->
<mxCell id="msg_self" edge="1" parent="1"
  style="edgeStyle=orthogonalEdgeStyle;curved=0;strokeColor=#2d862d;strokeWidth=1.5;endArrow=block;endFill=1;fontSize=10;fontColor=#2d862d;fontStyle=0;align=left;verticalAlign=bottom;labelBackgroundColor=none;rounded=0;"
  value="internalProcess()">
  <mxGeometry relative="1" as="geometry">
    <Array as="points">
      <mxPoint x="825" y="271"/>
      <mxPoint x="825" y="296"/>
    </Array>
    <mxPoint x="780" y="271" as="sourcePoint"/>
    <mxPoint x="778" y="296" as="targetPoint"/>
  </mxGeometry>
</mxCell>
```

- **`rounded=0`**: Bắt buộc thêm vào style để giữ vuông góc
- **`curved=0`**: KHÔNG cong
- Waypoints tạo hình chữ U nằm ngang (→ ↓ ←)
- Dùng `sourcePoint`/`targetPoint` (KHÔNG dùng source/target attribute)

### Note template (inline text, không border)

```xml
<!-- Note nhỏ gắn cạnh lifeline, không có box border -->
<mxCell id="note1" parent="1" vertex="1"
  style="text;html=1;align=left;verticalAlign=middle;fontSize=10;fontColor=#888888;fontStyle=2;fillColor=none;strokeColor=none;"
  value="Mô tả ngắn gọn logic">
  <mxGeometry x="520" y="285" width="250" height="20" as="geometry"/>
</mxCell>
```

- **Không có border** (`strokeColor=none;fillColor=none`)
- **fontStyle=2** (italic), `fontColor=#888888` (xám)
- Đặt bên phải message arrow hoặc cạnh lifeline

### "Route handler" self-call (màu xám nhạt, vai trò phụ)

Khi cần thể hiện xử lý nội bộ không quan trọng (route handler, internal processing), dùng màu xám:

```xml
<mxCell id="r6" edge="1" parent="1"
  style="edgeStyle=orthogonalEdgeStyle;curved=0;strokeColor=#D6D6D6;strokeWidth=1.5;endArrow=block;endFill=1;fontSize=10;fontColor=#D6D6D6;fontStyle=0;align=left;verticalAlign=bottom;labelBackgroundColor=none;rounded=1;fillColor=#f5f5f5;"
  value="Route handler">
  <mxGeometry relative="1" as="geometry">
    <Array as="points">
      <mxPoint x="403" y="727"/>
      <mxPoint x="403" y="752"/>
    </Array>
    <mxPoint x="359" y="727" as="sourcePoint"/>
    <mxPoint x="359" y="752" as="targetPoint"/>
  </mxGeometry>
</mxCell>
```

- `strokeColor=#D6D6D6` + `fontColor=#D6D6D6`: Xám nhạt → vai trò phụ
- `rounded=1`: Bo tròn nhẹ (khác với self-call chính)

### Fragment templates (ALT / OPT / LOOP / BREAK)

```xml
<!-- ALT fragment: dùng shape=mxgraph.sysml.package, align=left, labelX cho tab width -->
<mxCell id="frag_alt" parent="1" vertex="1"
  style="shape=mxgraph.sysml.package;html=1;overflow=fill;whiteSpace=wrap;fillColor=none;strokeColor=#cc0000;strokeWidth=1.5;fontSize=11;align=left;"
  value="&lt;b style=&quot;color:#cc0000;font-size:10px;&quot;&gt;alt: [license valid]&lt;/b&gt;">
  <mxGeometry x="60" y="650" width="850" height="180" as="geometry"/>
</mxCell>

<!-- LOOP fragment: thêm labelX để control tab width -->
<mxCell id="frag_loop" parent="1" vertex="1"
  style="shape=mxgraph.sysml.package;html=1;overflow=fill;whiteSpace=wrap;fillColor=none;strokeColor=#2d862d;strokeWidth=1.5;fontSize=11;align=left;labelX=100.69;"
  value="&lt;b style=&quot;color:#2d862d;font-size:10px;&quot;&gt;loop: [every 1 hour]&lt;/b&gt;">
  <mxGeometry x="60" y="480" width="850" height="150" as="geometry"/>
</mxCell>

<!-- OPT fragment: labelX lớn hơn nếu label dài -->
<mxCell id="frag_opt" parent="1" vertex="1"
  style="shape=mxgraph.sysml.package;html=1;overflow=fill;whiteSpace=wrap;fillColor=none;strokeColor=#e6a800;strokeWidth=1.5;fontSize=11;align=left;labelX=128.86;"
  value="&lt;b style=&quot;color:#e6a800;font-size:10px;&quot;&gt;opt:&amp;nbsp; Can call Server&lt;/b&gt;">
  <mxGeometry x="60" y="660" width="850" height="120" as="geometry"/>
</mxCell>
```

- **`align=left`**: Label căn trái
- **`labelX=xxx`**: Điều chỉnh độ rộng tab label (mặc định auto, set thủ công nếu label bị cắt)
- **HTML dùng `&quot;`** thay vì `'` cho style attribute trong value
- Fragment border: **nét liền** (`strokeWidth=1.5`)
- Chiều rộng bao trùm các lifeline liên quan

### ALT divider + else label

```xml
<!-- Dashed divider line inside ALT -->
<mxCell id="alt_div" parent="1" vertex="1"
  style="line;strokeWidth=1;dashed=1;dashPattern=8 4;strokeColor=#cc0000;fillColor=none;" value="">
  <mxGeometry x="270" y="506" width="525" height="5" as="geometry"/>
</mxCell>

<!-- Else label -->
<mxCell id="alt_else" parent="1" vertex="1"
  style="text;html=1;fontSize=9;fontColor=#cc0000;align=left;fontStyle=1;"
  value="[license invalid]">
  <mxGeometry x="270" y="506" width="100" height="18" as="geometry"/>
</mxCell>
```

- Divider `height=5` (không phải 1) — cho phép overlap với label
- Label `fontColor` = fragment `strokeColor`
- Label `fontStyle=1` (bold)

### Destroy marker (X)

```xml
<mxCell id="destroy1" parent="1" vertex="1"
  style="shape=cross;whiteSpace=wrap;html=1;fillColor=none;strokeColor=#cc0000;strokeWidth=2;size=10;"
  value="">
  <mxGeometry x="160" y="850" width="20" height="20" as="geometry"/>
</mxCell>
```

## Bảng màu Participant (Color Scheme)

| Vai trò | fillColor | strokeColor | fontColor | Khi nào dùng |
|---------|-----------|-------------|-----------|--------------|
| Client / User / App UI | `#5b9bd5` | `#4a8ac4` | `#ffffff` | Người dùng, app client, mobile UI |
| SDK / Core Manager | `#1a3a5c` | `#0f2e4d` | `#ffffff` | SDK module, core logic, manager |
| Storage / Keystore | `#00bcd4` | `#00a5bb` | `#ffffff` | Keystore, secure storage, cache |
| Server / Backend | `#7b68ee` | `#6a5acd` | `#ffffff` | License server, API server, backend |
| External / Gateway | `#ff9800` | `#e68a00` | `#ffffff` | Payment gateway, 3rd-party, external API |
| Database | `#4caf50` | `#43a047` | `#ffffff` | Database, persistent storage |
| Security / Auth | `#e91e63` | `#c2185b` | `#ffffff` | Auth service, security module |
| Error state | `#990000` | `#660000` | `#ffffff` | Error / Kill / Terminate |

> **Lưu ý:** Tất cả participant box dùng **fontColor trắng** trên nền đậm. `rounded=0` (sắc cạnh).

## Bảng màu Message (Color Scheme)

| Loại Message | strokeColor | fontColor | Style | Mô tả |
|-------------|-------------|-----------|-------|-------|
| Sync call (internal) | `#2d862d` | `#2d862d` | `endArrow=block;endFill=1;fontStyle=1;` | Gọi đồng bộ giữa các component |
| Return (success) | `#2d862d` | `#2d862d` | `dashed=1;endArrow=open;endFill=0;` | Response thành công |
| External call | `#e6a800` | `#e6a800` | `endArrow=block;endFill=1;fontStyle=1;` | Gọi ra bên ngoài (3rd-party) |
| External return | `#e6a800` | `#e6a800` | `dashed=1;endArrow=open;endFill=0;` | Response từ bên ngoài |
| Callback / Event | `#e64a19` | `#e64a19` | `endArrow=block;endFill=1;fontStyle=1;` | Callback, event notification |
| Error / Fail | `#cc0000` | `#cc0000` | `dashed=1;endArrow=open;endFill=0;fontStyle=1;` | Lỗi, fail response |
| Internal (phụ) | `#D6D6D6` | `#D6D6D6` | `endArrow=block;endFill=1;fontStyle=0;rounded=1;` | Route handler, internal logic |

> **Quy tắc:** `strokeColor` và `fontColor` của message PHẢI cùng màu. `labelBackgroundColor=none` (không nền trắng).

## Bảng màu Fragment

| Fragment | strokeColor | Khi nào dùng |
|----------|-------------|--------------|
| `alt` (if/else) | `#cc0000` | Điều kiện rẽ nhánh, error case |
| `opt` (optional) | `#e6a800` | Khối tùy chọn |
| `loop` | `#2d862d` | Vòng lặp |
| `break` | `#cc0000` | Thoát khỏi vòng lặp / lỗi |
| `par` (parallel) | `#7b68ee` | Xử lý song song |
| `critical` | `#e91e63` | Critical section |

## Quy trình thực hiện

### Bước 1: Xác định context
- Đọc source code hoặc spec liên quan
- Xác định **use case** cụ thể cần vẽ (login flow, init sequence, API call, v.v.)
- Liệt kê các **actors/participants** tham gia vào flow

### Bước 2: Liệt kê tương tác
- Lập danh sách các messages theo thứ tự thời gian
- Xác định message type: sync, return, external, callback, error
- Xác định fragments cần dùng: alt, loop, opt, break
- **Đánh số thứ tự steps** liên tục: 1, 2, 3, ... (mỗi step = 1 interaction quan trọng)

### Bước 3: Tính toán layout
- Participants cách nhau **~180-210px** theo chiều ngang
- Participant box đầu tiên bắt đầu từ `x=80`
- Mỗi step cách nhau **50px** theo chiều dọc
- Step numbers bắt đầu từ `y=150` (dưới participant boxes)
- **KHÔNG dùng activation box** — giữ diagram sạch
- Fragment box bao trùm các steps liên quan, dùng `labelX` cho tab width

### Bước 4: Vẽ diagram
- Tạo XML theo template trên
- Thứ tự render trong XML:
  1. Title + Subtitle
  2. Lifelines (edge, có màu theo participant)
  3. Participants (nằm TRÊN lifeline)
  4. Step numbers + Horizontal guide lines
  5. Messages (dùng sourcePoint/targetPoint)
  6. Self-calls
  7. Notes
  8. Fragments
- Gọi `mcp_drawio_open_drawio_xml` với content là XML
- **KHÔNG set `dark` parameter** — để auto

### Bước 5: Save file (nếu user yêu cầu)
- Lưu XML ra file `.drawio` hoặc `.drawio.xml` trong thư mục `docs/` của project

## Ví dụ hoàn chỉnh

```xml
<mxGraphModel dx="2593" dy="1436" grid="1" gridSize="10" guides="1" tooltips="1" connect="1" arrows="1" fold="1" page="0" pageScale="1" pageWidth="827" pageHeight="1169" math="0" shadow="0">
  <root>
    <mxCell id="0"/>
    <mxCell id="1" parent="0"/>

    <!-- Title + Subtitle -->
    <mxCell id="title" parent="1" vertex="1"
      style="text;html=1;align=center;verticalAlign=middle;fontStyle=1;fontSize=24;fontColor=#1a1a2e;"
      value="CShield SDK — License Validation">
      <mxGeometry x="350" y="10" width="700" height="35" as="geometry"/>
    </mxCell>
    <mxCell id="subtitle" parent="1" vertex="1"
      style="text;html=1;align=center;verticalAlign=middle;fontSize=11;fontColor=#888888;fontStyle=2;"
      value="License Module — Full Lifecycle: Init, Runtime, Heartbeat, Auto-Renewal">
      <mxGeometry x="350" y="40" width="700" height="20" as="geometry"/>
    </mxCell>

    <!-- Lifelines (edge, có màu theo participant) -->
    <mxCell id="ll_client" edge="1" parent="1"
      style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;dashed=1;dashPattern=12 12;strokeColor=#5B9BD5;">
      <mxGeometry relative="1" as="geometry">
        <mxPoint x="145" y="120" as="sourcePoint"/>
        <mxPoint x="145" y="1737" as="targetPoint"/>
      </mxGeometry>
    </mxCell>
    <mxCell id="ll_server" edge="1" parent="1"
      style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;dashed=1;dashPattern=12 12;strokeColor=#1A3A5C;">
      <mxGeometry relative="1" as="geometry">
        <mxPoint x="349" y="120" as="sourcePoint"/>
        <mxPoint x="349" y="1737" as="targetPoint"/>
      </mxGeometry>
    </mxCell>
    <mxCell id="ll_sdk" edge="1" parent="1"
      style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;dashed=1;dashPattern=12 12;strokeColor=#00BCD4;">
      <mxGeometry relative="1" as="geometry">
        <mxPoint x="559" y="120" as="sourcePoint"/>
        <mxPoint x="559" y="1737" as="targetPoint"/>
      </mxGeometry>
    </mxCell>

    <!-- Participants (rounded=0, màu đậm, text trắng, 2 dòng) -->
    <mxCell id="p_client" parent="1" vertex="1"
      style="rounded=0;whiteSpace=wrap;html=1;fillColor=#5b9bd5;strokeColor=#4a8ac4;fontStyle=1;fontSize=11;fontColor=#ffffff;verticalAlign=middle;"
      value="HTTP Client&#10;(Consumer)">
      <mxGeometry x="80" y="70" width="130" height="50" as="geometry"/>
    </mxCell>
    <mxCell id="p_server" parent="1" vertex="1"
      style="rounded=0;whiteSpace=wrap;html=1;fillColor=#1a3a5c;strokeColor=#0f2e4d;fontStyle=1;fontSize=11;fontColor=#ffffff;verticalAlign=middle;"
      value="Customer Server&#10;(Host App)">
      <mxGeometry x="280" y="70" width="140" height="50" as="geometry"/>
    </mxCell>
    <mxCell id="p_cshield" parent="1" vertex="1"
      style="rounded=0;whiteSpace=wrap;html=1;fillColor=#00bcd4;strokeColor=#00a5bb;fontStyle=1;fontSize=11;fontColor=#ffffff;verticalAlign=middle;"
      value="CShield SDK&#10;(Factory)">
      <mxGeometry x="490" y="70" width="140" height="50" as="geometry"/>
    </mxCell>

    <!-- Step numbers + Guide lines -->
    <mxCell id="step_1" parent="1" vertex="1"
      style="text;html=1;align=center;verticalAlign=middle;fontSize=12;fontColor=#4a90d9;fontStyle=1;"
      value="1">
      <mxGeometry x="20" y="155" width="30" height="20" as="geometry"/>
    </mxCell>
    <mxCell id="hline_1" parent="1" vertex="1"
      style="line;strokeWidth=1;strokeColor=#e0e0e0;fillColor=none;" value="">
      <mxGeometry x="50" y="165" width="1300" height="1" as="geometry"/>
    </mxCell>

    <!-- Messages (dùng sourcePoint/targetPoint) -->
    <mxCell id="m1" edge="1" parent="1"
      style="edgeStyle=none;strokeColor=#2D862D;strokeWidth=1.5;endArrow=block;endFill=1;fontSize=10;fontColor=#2d862d;fontStyle=1;labelBackgroundColor=none;align=center;"
      value="CShield.create(config)">
      <mxGeometry relative="1" as="geometry">
        <mxPoint y="-8" as="offset"/>
        <mxPoint x="356" y="225" as="sourcePoint"/>
        <mxPoint x="550" y="225" as="targetPoint"/>
      </mxGeometry>
    </mxCell>

    <!-- Self-call (vuông góc, sourcePoint/targetPoint) -->
    <mxCell id="m2a" edge="1" parent="1"
      style="edgeStyle=orthogonalEdgeStyle;curved=0;strokeColor=#2d862d;strokeWidth=1.5;endArrow=block;endFill=1;fontSize=10;fontColor=#2d862d;fontStyle=0;align=left;verticalAlign=bottom;labelBackgroundColor=none;rounded=0;"
      value="Decode JWT + verify RS256">
      <mxGeometry relative="1" as="geometry">
        <Array as="points">
          <mxPoint x="825" y="271"/>
          <mxPoint x="825" y="296"/>
        </Array>
        <mxPoint x="780" y="271" as="sourcePoint"/>
        <mxPoint x="778" y="296" as="targetPoint"/>
      </mxGeometry>
    </mxCell>

    <!-- Fragment ALT -->
    <mxCell id="frag_alt" parent="1" vertex="1"
      style="shape=mxgraph.sysml.package;html=1;overflow=fill;whiteSpace=wrap;fillColor=none;strokeColor=#cc0000;strokeWidth=1.5;fontSize=11;align=left;"
      value="&lt;b style=&quot;color:#cc0000;font-size:10px;&quot;&gt;alt: [license valid]&lt;/b&gt;">
      <mxGeometry x="268" y="340" width="532" height="219" as="geometry"/>
    </mxCell>

    <!-- Note inline -->
    <mxCell id="note_init" parent="1" vertex="1"
      style="text;html=1;align=left;verticalAlign=middle;fontSize=10;fontColor=#888888;fontStyle=2;fillColor=none;strokeColor=none;"
      value="Factory Pattern: No instance = No middleware">
      <mxGeometry x="47" y="169" width="250" height="20" as="geometry"/>
    </mxCell>

    <!-- Phase label -->
    <mxCell id="phase1" parent="1" vertex="1"
      style="text;html=1;align=left;verticalAlign=middle;fontStyle=1;fontSize=13;fontColor=#1a3a5c;fillColor=none;strokeColor=none;"
      value="PHASE 1: Server Startup">
      <mxGeometry x="30" y="130" width="250" height="20" as="geometry"/>
    </mxCell>
  </root>
</mxGraphModel>
```

## Lưu ý quan trọng

1. **Mỗi mxCell PHẢI có unique id** — không trùng nhau
2. **Edge cells phải có `<mxGeometry relative="1" as="geometry"/>`** — bắt buộc
3. **Dùng `&#10;` cho newline** trong value, KHÔNG dùng `\n`
4. **Dùng `&amp;` thay `&`**, `&lt;` thay `<`, `&gt;` thay `>` trong value attribute
5. **MỌI participant PHẢI có lifeline chạy XUYÊN SUỐT** từ dưới participant box đến cuối flow
6. **Lifeline dùng `edge`** (KHÔNG dùng vertex), `dashPattern=12 12`, **strokeColor theo participant**
7. **KHÔNG dùng activation box** — diagram sạch hơn, dùng sourcePoint/targetPoint trực tiếp
8. **Messages dùng `sourcePoint`/`targetPoint`** (KHÔNG dùng `source`/`target` attribute trỏ vào activation box)
9. **Participants dùng `rounded=0`** (sắc cạnh), 2 dòng text (tên + vai trò), `fontColor=#ffffff`, `fontSize=11`
10. **Participants cách nhau ~180-210px**, bắt đầu từ `x=80`
11. **Step numbers bên trái** (`x=20`), mỗi step cách **50px** theo chiều dọc
12. **Horizontal guide lines** (`strokeColor=#e0e0e0`, `width=1300`) cho mỗi step
13. **Message colors PHẢI nhất quán**: `strokeColor` = `fontColor` cùng màu
14. **labelBackgroundColor=none** — label message KHÔNG có nền trắng
15. **Self-call dùng `curved=0;rounded=0;`** — mũi tên vuông góc, dùng sourcePoint/targetPoint
16. **Notes: inline text** (`fillColor=none;strokeColor=none`), `fontStyle=2` (italic), `fontColor=#888888`
17. **Fragments: `align=left`** + `labelX=xxx` để control tab width
18. **Fragment HTML dùng `&quot;`** thay vì `'` cho style attribute
19. **fontSize: title=24, subtitle=11, participant=11, message=10, note=10, step number=12**
20. **Tránh XML comment dùng `--`** — draw.io không parse được
21. **Phase labels** dùng `fontColor` trùng participant chính của phase đó

## Ví dụ câu lệnh từ user

```
"Vẽ sequence diagram cho login flow"
"Tạo biểu đồ tuần tự cho API authentication bằng draw.io"
"Vẽ sequence diagram tương tác giữa client, server, database"
"Vẽ draw.io sequence diagram cho init SDK flow"
"Vẽ luồng request/response giữa các microservices"
```
