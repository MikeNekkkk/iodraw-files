```mermaid
flowchart TD
    A[Bắt đầu] --> B[Đọc dữ liệu đầu vào]
    
    B --> B1[Đọc file khoang_cachhhh.csv<br/>Thông tin các cạnh và khoảng cách]
    B --> B2[Đọc file checkBĐ2.csv<br/>Tọa độ các điểm<br/>Point, Latitude, Longitude]
    B --> B3[Đọc file KCchimbay1.csv<br/>Giá trị heuristic h_n<br/>Point, h_n km]
    
    B1 --> C[Xử lý và chuẩn hóa dữ liệu]
    B2 --> C
    B3 --> C
    
    C --> C1[Loại bỏ khoảng trắng thừa<br/>edges_df.columns.str.strip<br/>edges_df.Từ.str.strip<br/>edges_df.Đến.str.strip]
    C1 --> C2[Chuyển đơn vị từ mét sang km<br/>distance = row[2] / 1000]
    
    C2 --> D[Xây dựng cấu trúc dữ liệu]
    
    D --> D1[Tạo đồ thị vô hướng<br/>graph[from_node][to_node] = distance<br/>graph[to_node][from_node] = distance]
    D --> D2[Tạo từ điển tọa độ điểm<br/>points[node] = lat, lon]
    D --> D3[Tạo từ điển giá trị heuristic<br/>hn_values[point] = h_n value]
    
    D1 --> E[Khởi tạo tham số]
    D2 --> E
    D3 --> E
    
    E --> E1[Đặt điểm xuất phát: Diem_Dich<br/>Đặt điểm đích: Điem_den_1]
    
    E1 --> F{Kiểm tra tính hợp lệ<br/>start_node in graph?<br/>end_node in graph?}
    
    F -->|Không hợp lệ| G[Báo lỗi:<br/>Node không tồn tại trong đồ thị]
    F -->|Hợp lệ| H[Lựa chọn thuật toán]
    
    H --> H1[Thuật toán Dijkstra]
    H --> H2[Thuật toán A*]
    
    %% Dijkstra Algorithm Flow
    H1 --> I1[Khởi tạo Dijkstra]
    I1 --> I2[path[node] = ∞ cho tất cả node<br/>path[start] = 0<br/>queue = [0, start]<br/>came_from = {}]
    I2 --> I3[Bắt đầu đo thời gian<br/>start_time = time.perf_counter_ns]
    I3 --> I4[Vòng lặp chính while queue]
    I4 --> I5[current_dist, current_node = heapq.heappop]
    I5 --> I6{current_node == end?}
    I6 -->|Có| J1[Thoát vòng lặp]
    I6 -->|Không| I7[Duyệt tất cả neighbor của current_node]
    I7 --> I8[distance = current_dist + graph[current][neighbor]]
    I8 --> I9{distance < path[neighbor]?}
    I9 -->|Có| I10[Cập nhật path[neighbor] = distance<br/>came_from[neighbor] = current_node<br/>heapq.heappush queue, distance, neighbor]
    I9 -->|Không| I11[Bỏ qua neighbor này]
    I10 --> I12[loop_count += 1]
    I11 --> I12
    I12 --> I4
    
    %% A* Algorithm Flow  
    H2 --> K1[Khởi tạo A*]
    K1 --> K2[queue = [hn_values[start], start]<br/>path[start] = 0<br/>path_heu[start] = hn_values[start]<br/>adj_node = {}<br/>visited = set]
    K2 --> K3[Bắt đầu đo thời gian<br/>start_time = time.perf_counter_ns]
    K3 --> K4[Vòng lặp chính while queue]
    K4 --> K5[f_value, current = heapq.heappop]
    K5 --> K6{current in visited?}
    K6 -->|Có| K4
    K6 -->|Không| K7[visited.add current]
    K7 --> K8{current == goal?}
    K8 -->|Có| J2[Thoát vòng lặp]
    K8 -->|Không| K9[Duyệt neighbor của current]
    K9 --> K10{neighbor in visited?}
    K10 -->|Có| K11[Bỏ qua neighbor]
    K10 -->|Không| K12[g_new = path[current] + graph[current][neighbor]<br/>h_new = hn_values.get neighbor, 0<br/>f_new = g_new + h_new]
    K11 --> K9
    K12 --> K13{neighbor not in path OR<br/>f_new < path_heu[neighbor]?}
    K13 -->|Có| K14[path[neighbor] = g_new<br/>path_heu[neighbor] = f_new<br/>adj_node[neighbor] = current<br/>heapq.heappush queue, f_new, neighbor]
    K13 -->|Không| K15[Bỏ qua neighbor]
    K14 --> K4
    K15 --> K4
    
    %% Path Reconstruction
    J1 --> L1[Truy vết đường đi Dijkstra<br/>route = []<br/>current = end]
    L1 --> L2[while current != start:<br/>route.append current<br/>current = came_from[current]]
    L2 --> L3[route.append start<br/>route.reverse]
    
    J2 --> L4[Truy vết đường đi A*<br/>route = []<br/>current = goal]
    L4 --> L5[while current != start:<br/>route.append current<br/>current = adj_node[current]]
    L5 --> L6[route.append start<br/>route.reverse]
    
    L3 --> M[Kết thúc đo thời gian<br/>end_time = time.perf_counter_ns]
    L6 --> M
    
    M --> N[Tính toán và xuất kết quả]
    
    N --> N1[Tổng quãng đường: path[end] km<br/>Dijkstra: path[end]<br/>A*: path[goal]]
    N --> N2[Số vòng lặp/điểm thăm:<br/>Dijkstra: loop_count<br/>A*: len visited]
    N --> N3[Thời gian thực thi:<br/>end_time - start_time nanoseconds]
    N --> N4[Lộ trình đầy đủ:<br/> -> .join route]
    
    N1 --> O[Kết thúc]
    N2 --> O
    N3 --> O
    N4 --> O
    
    G --> O
    
    %% Styling
    classDef startEnd fill:#e1f5fe,stroke:#01579b,stroke-width:3px
    classDef process fill:#f3e5f5,stroke:#4a148c,stroke-width:2px
    classDef decision fill:#fff8e1,stroke:#ff8f00,stroke-width:2px
    classDef algorithm fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    classDef data fill:#fce4ec,stroke:#880e4f,stroke-width:2px
    
    class A,O startEnd
    class B1,B2,B3,C1,C2,D1,D2,D3 data
    class H1,H2,I1,K1 algorithm
    class F,I6,I9,K6,K8,K10,K13 decision
    class I4,I5,I7,I8,I10,I12,K4,K5,K7,K9,K12,K14,L1,L2,L4,L5,M,N process
```