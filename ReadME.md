## Đồ Hình backbone Mạng AT&T

## Sử dụng tệp đồ thị, thuật toán, tính toán Số lượng demands, tối ưu hóa mạng và tạo đồ hình backbone của mạng.

### PHẦN 1: Bài toán

#### 1.
**Mô phỏng định tuyến và tối ưu hóa mạng trục (Backbone Network) AT&T.**

#### 2. Đầu vào (Input)
*   **Dữ liệu cấu trúc mạng (`AttMpls.gml`):** Chứa danh sách các node (nút mạng) và edges (cạnh/liên kết).
    *   Thuộc tính Node: Kinh độ (Longitude), Vĩ độ (Latitude).
    *   Thuộc tính Edge: Kết nối vật lý giữa các node.
*   **Dữ liệu nhu cầu lưu lượng (`AttDemand.csv`)
    *   Cấu trúc: `[Sequence Number, Source ID, Target ID, Bandwidth (Mbps)]`.
*   **Tham số cố định:**
    *   Bán kính Trái đất: $R = 6371$ km.

#### 3. Các Constraints
1.  **Tính khoảng cách:** Tính khoảng cách 2 node bằng công thức **Haversine**.
2.  **Quy hoạch dung lượng (Capacity Rules):** Dựa theo đề bài dung lượng (Capacity) của mỗi cạnh phụ thuộc vào khoảng cách ($d$):
    *   $0 \le d < 1000$ km $\rightarrow$ Cap = 100 Mbps.
    *   $1000 \le d < 2000$ km $\rightarrow$ Cap = 200 Mbps.
    *   $2000 \le d < 3000$ km $\rightarrow$ Cap = 300 Mbps.
    *   (Cần xử lý trường hợp ngoại lệ nếu $d \ge 3000$).
3.  **Nguyên tắc định tuyến cơ bản:**
    *   Xử lý tuần tự (Sequential) từ trên xuống dưới theo file CSV.
    *   Một demand chỉ được chấp nhận nếu tìm được đường đi từ Source đến Target mà trên **tất cả các cạnh** của đường đi đó: `Dung lượng còn dư (Residual Capacity) >= Bandwidth yêu cầu`.

#### 4. Đầu ra yêu cầu (Output Requirements)
1.  **Về mặt hình ảnh**
    *   Hình ảnh đồ thị mạng lưới với vị trí địa lý thực.
    *   Phân biệt cạnh thuộc cây khung nhỏ nhất (MST) và cạnh thường.
2.  **Thống kê định tuyến:**
    *   Tổng số demand được chấp nhận ($N$).
    *   Danh sách chi tiết các demand được chấp nhận (ID, Route, v.v.).
3.  **Thống kê hiệu năng:**
    *   Số lượng liên kết (link) có mức sử dụng > 70%.
4.  **Giải pháp tối ưu:**
    *   Đề xuất giải pháp để tăng $N$ lên mức tối đa.

---

### PHẦN 2: Danh sách cần làm


#### Phần 1: Xử lý dữ liệu & Đồ thị
*  Đọc file `.gml` và tạo đối tượng đồ thị (dùng NetworkX).
*  Viết hàm `haversine(lat1, lon1, lat2, lon2)` trả về km.
* Duyệt toàn bộ các cạnh, tính khoảng cách và gán trọng số `weight='distance'`.
*  Duyệt toàn bộ các cạnh, gán thuộc tính `capacity` và khởi tạo `residual` dựa trên khoảng cách.

#### Phần 2: Thuật toán & Logic mạng
  **Tìm MST:** Sử dụng thuật toán Kruskal hoặc Prim để tìm cây khung nhỏ nhất.
 **Hàm tìm đường (Routing):** Viết hàm tìm đường đi ngắn nhất (Dijkstra) nhưng có điều kiện lọc: `if residual >= demand_bandwidth`.
 **Chạy mô phỏng (Simulation Loop):**
    *   Đọc file CSV.
    *   Vòng lặp duyệt từng demand.
    *   Cập nhật `residual` nếu tìm được đường.
    *   Đếm số thành công/thất bại.

#### Phần 3: Tổng kết chương trình & Báo cáo
*  Vẽ đồ thị với Matplotlib: Node đúng vị trí kinh/vĩ độ (cần để ý nét thường nét đứt khi vẽ cạnh MST)
* In kết quả thống kê dạng text/JSON như đề bài yêu cầu.
*  Tính toán số lượng link bị nghẽn (>70%).

---
[![](https://mermaid.ink/img/pako:eNqNVl1r21YY_isvgg6bOZ7tfCgWNCO126TEyUrsZXR2Lk4kRTqzfKRKR2nSkIuyi12UsYZdjrGZUkqzhXZbYcxm9EKh_0P_ZO85kmWpH3S-iKyT8zzP-_G8Lz5VdNcwFU05dNz7uk18Dr32gAF-uhzfSv0b8eQFh6vzePIs3C_DwsIadFxibGx3-leP4-n3OhxSx4R1zrc9J6haI2c_wae3MkSru_c2om2OCDOqenCUYpK_QXhg-cSzYaBs2fH0Fwo8noxdEOgfgePRIyjdZpRT4tAHhFOXlQdKgp0po5pUbhFHb9OA93vRJbNhaLvx5AmzQI_Gug2b5Mj0A8pM0G1XnoGOUszen7PNGCTdehBQi7WI19-Ixgz_5xGd8hMMynRhN8S0igo5ngwqiUT4u2bQfytFPKJGSBy4nnEPlJTEZMaAvVOhu9FFKIJ-FkJdg54fT__Q4V5IGNjRXyRfllRSyt-izNju9lC-F_0-AvwKpS0_DIbE-eyOT0flTFV80tsS2fbJfYTtxZN_hStmDdEkB4uec3BoPH3IKliAPxk4mBZNzsXt5_z_p9NAzugVeEj_A9ZTdP8RtpCHJ_HkNYPSrhtyyqxC60V0MkxM1OQtN2Tc9DFcBE_-5nBAJVS4-fUIdrDKtUKieVRqXNeTg3DaEtkkhgXuuxiQg6b4_GwOzhswRSEH3E1LvmGmfsdwOvHk6UnGJoLyEguVur5egXbAK3Djq2ITMnzWwDuE27MOXp2_eRlPfxZ1Osd6Wzi1DJiNOhxKbfrNMOA-KQ8G7OqxaE8IQ3x8xzS4aVhmNbPd2nXULcgWXSAkRVJSk9syDRHOl55BuNmhbNgXFvwNWeAo-jVn6NxwYf2iC1YMOac450o7qbt-kmvnzcsQVaNXiMHWf5BhDklnTd8Rdbr6Fm-Kpu_Ap1AvZCmuFBv-sQpspWHMa9BxrVuEOv0Nm8rKX2Z-ORAbi4uy6GjmQqgp6H3aHx2QRfgElnDmBSeGMowu8KAnBAALVdyIOUPuoM020XJcOricrUm8wAPpJ7Epr0EQTy_BiKdPRZoYPD5EUwp1y3AJi5icTWrZYgOnU4dzhkTnEgpG9A-yrIFau1akyQMlFS4hJrZV6GBMtxn4BHZwMAhGFsjVLfpQgW5GfS-MxmKJPslXN88iab_w-LpjuUls04dwHMoJ4TY-L_Hpiq3OZyWE0l5bQ43JCw-O5ZDORlZaycAusOKQpvyJcUOGr_0WWmF8ki7CUW6hHcXTn-iHpN9dkgmdZL7JjFJ_S7YQp2Gi75eVimL51FA07odmRRmZ_oiIV-VUgAcKLpeROVA0_GoQfyi8cYYYj7CvXXc0g_luaNmKdkicAN9COYdtStB68ysYkenLfilaQ61LDkU7VY4VbUFt1KqqulpfVuu1xsriUrOinOBxvdZUq-pyrbbcbDZUPG6eVZQHUnelqi7WVtXaylKzvlprLK9UFNOg3PW3k58l8tfJ2X_8uike?type=png)](https://mermaid.live/edit#pako:eNqNVl1r21YY_isvgg6bOZ7tfCgWNCO126TEyUrsZXR2Lk4kRTqzfKRKR2nSkIuyi12UsYZdjrGZUkqzhXZbYcxm9EKh_0P_ZO85kmWpH3S-iKyT8zzP-_G8Lz5VdNcwFU05dNz7uk18Dr32gAF-uhzfSv0b8eQFh6vzePIs3C_DwsIadFxibGx3-leP4-n3OhxSx4R1zrc9J6haI2c_wae3MkSru_c2om2OCDOqenCUYpK_QXhg-cSzYaBs2fH0Fwo8noxdEOgfgePRIyjdZpRT4tAHhFOXlQdKgp0po5pUbhFHb9OA93vRJbNhaLvx5AmzQI_Gug2b5Mj0A8pM0G1XnoGOUszen7PNGCTdehBQi7WI19-Ixgz_5xGd8hMMynRhN8S0igo5ngwqiUT4u2bQfytFPKJGSBy4nnEPlJTEZMaAvVOhu9FFKIJ-FkJdg54fT__Q4V5IGNjRXyRfllRSyt-izNju9lC-F_0-AvwKpS0_DIbE-eyOT0flTFV80tsS2fbJfYTtxZN_hStmDdEkB4uec3BoPH3IKliAPxk4mBZNzsXt5_z_p9NAzugVeEj_A9ZTdP8RtpCHJ_HkNYPSrhtyyqxC60V0MkxM1OQtN2Tc9DFcBE_-5nBAJVS4-fUIdrDKtUKieVRqXNeTg3DaEtkkhgXuuxiQg6b4_GwOzhswRSEH3E1LvmGmfsdwOvHk6UnGJoLyEguVur5egXbAK3Djq2ITMnzWwDuE27MOXp2_eRlPfxZ1Osd6Wzi1DJiNOhxKbfrNMOA-KQ8G7OqxaE8IQ3x8xzS4aVhmNbPd2nXULcgWXSAkRVJSk9syDRHOl55BuNmhbNgXFvwNWeAo-jVn6NxwYf2iC1YMOac450o7qbt-kmvnzcsQVaNXiMHWf5BhDklnTd8Rdbr6Fm-Kpu_Ap1AvZCmuFBv-sQpspWHMa9BxrVuEOv0Nm8rKX2Z-ORAbi4uy6GjmQqgp6H3aHx2QRfgElnDmBSeGMowu8KAnBAALVdyIOUPuoM020XJcOricrUm8wAPpJ7Epr0EQTy_BiKdPRZoYPD5EUwp1y3AJi5icTWrZYgOnU4dzhkTnEgpG9A-yrIFau1akyQMlFS4hJrZV6GBMtxn4BHZwMAhGFsjVLfpQgW5GfS-MxmKJPslXN88iab_w-LpjuUls04dwHMoJ4TY-L_Hpiq3OZyWE0l5bQ43JCw-O5ZDORlZaycAusOKQpvyJcUOGr_0WWmF8ki7CUW6hHcXTn-iHpN9dkgmdZL7JjFJ_S7YQp2Gi75eVimL51FA07odmRRmZ_oiIV-VUgAcKLpeROVA0_GoQfyi8cYYYj7CvXXc0g_luaNmKdkicAN9COYdtStB68ysYkenLfilaQ61LDkU7VY4VbUFt1KqqulpfVuu1xsriUrOinOBxvdZUq-pyrbbcbDZUPG6eVZQHUnelqi7WVtXaylKzvlprLK9UFNOg3PW3k58l8tfJ2X_8uike)






