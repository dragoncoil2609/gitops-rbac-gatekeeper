# Giải thích cho 2 câu hỏi lấy điểm cộng (Challenge Lab 10)

### 1. Vì sao guardrail cũ tự áp cho team B mà không cần viết luật mới?
Tại Lab 1, chúng ta đã triển khai các chính sách (policies) bảo mật bằng Gatekeeper thông qua các `Constraint` (ví dụ: `K8sContainerLimits`, `K8sBlockHostNetwork`). 
Trong Kubernetes, Admission Webhook (như Gatekeeper) chặn các Request (như tạo Pod) ở cấp độ toàn Cụm (Cluster-wide). Các constraint mà chúng ta đã cấu hình (trong thư mục `gatekeeper/constraints`) không bị giới hạn riêng cho namespace `demo`, nên bất kỳ namespace mới nào được tạo ra (bao gồm cả `payments`) đều tự động kế thừa và bị kiểm duyệt bởi các luật bảo vệ này (Guardrails) ngay lập tức.

### 2. Role/RoleBinding khác ClusterRoleBinding ra sao để giữ cô lập?
- **Role và RoleBinding**: Là các resource có phạm vi trong 1 Namespace cụ thể (Namespaced-scoped). Khi dùng `RoleBinding` gán `Role` cho user `payments-dev` bên trong namespace `payments`, user đó chỉ có thể thao tác tài nguyên BÊN TRONG namespace `payments` mà thôi.
- **ClusterRoleBinding**: Là resource có phạm vi toàn cụm (Cluster-scoped). Nếu ta dùng `ClusterRoleBinding`, user sẽ được cấp quyền trên TẤT CẢ các namespace, dẫn đến rủi ro "vượt rào" sang phá phách namespace của team khác (như `demo`), phá vỡ tính cô lập. Do đó, Least-privilege bắt buộc phải dùng Role/RoleBinding bó gọn trong 1 Namespace.
