# Bước 1: Thiết kế giải pháp (I/O)

**1. Phân tích Đầu vào (Input):**
*   **Dữ liệu thô:** Tin nhắn khiếu nại, yêu cầu bảo hành của khách hàng gửi qua Fanpage hoặc Zalo của TechShop (thường viết tự do, lộn xộn, không theo form mẫu).
*   **Vấn đề/Bẫy dữ liệu:** Khách hàng thường quên cung cấp Mã đơn hàng hoặc Số điện thoại. Nếu AI không được trang bị kỹ năng xử lý lỗi, nó sẽ tự bịa ra thông tin hoặc làm hỏng cấu trúc dữ liệu.

**2. Phân tích Đầu ra (Output):**
*   **Cấu trúc mong muốn:** Chuỗi dữ liệu chuẩn `JSON` để tích hợp thẳng vào phần mềm quản trị ERP/CRM của cửa hàng.
*   **Các trường dữ liệu cần trích xuất (Keys):** `Customer_Name` (Tên), `Phone` (Số điện thoại), `Order_ID` (Mã đơn hàng), `Product` (Tên sản phẩm), `Issue` (Lỗi kỹ thuật), `Status` (Trạng thái).
*   **Logic xử lý lỗi (Error Handling):** 
    * Nếu thiếu thông tin bất kỳ: Ghi chữ `"NULL"`.
    * Nếu thiếu `Order_ID` hoặc `Phone`: Gán trường `Status` là `"CẦN XÁC MINH"`. 
    * Nếu đủ thông tin cơ bản: Gán trường `Status` là `"HỢP LỆ"`.

---

# Bước 2: Viết prompt theo khung CREATE và chạy thử

Dưới đây là câu lệnh hoàn chỉnh đã được thiết kế sẵn. Câu lệnh này ép AI phải làm việc như một cỗ máy trích xuất dữ liệu tự động.

## 📝 BẢN THIẾT KẾ PROMPT CHUẨN

> **[C - Context]** Bạn là Chuyên gia Xử lý Dữ liệu Hệ thống tại cửa hàng công nghệ TechShop.
> 
> **[R - Request]** Nhiệm vụ của bạn là đọc các tin nhắn yêu cầu bảo hành thô của khách hàng và trích xuất thông tin thành dữ liệu có cấu trúc.
> 
> **[E - Explanation]** 
> - **Ràng buộc an toàn:** Tuyệt đối không tự bịa đặt thông tin. Nếu khách hàng không nhắc đến một thông tin nào đó, bắt buộc điền giá trị `"NULL"`.
> - **Điều kiện logic:** Nếu khách hàng không cung cấp Mã đơn hàng (`Order_ID`) hoặc Số điện thoại (`Phone`), hãy gán trường `Status` thành `"CẦN XÁC MINH"`. Nếu có đủ, gán `"HỢP LỆ"`.
> 
> **[A - Action & Few-shot]** Trích xuất các trường: `Customer_Name`, `Phone`, `Order_ID`, `Product`, `Issue`, `Status`.
> Hãy học quy luật trích xuất từ 2 ví dụ mẫu sau:
> 
> *Ví dụ 1 (Dữ liệu đầy đủ):*
> Input: "Chào shop, mình là Tuấn, sđt 0901234567. Con chuột Logitech G102 mua hôm qua bị double click. Đơn hàng #TS9921."
> Output:
> ```json
> {
>   "Customer_Name": "Tuấn",
>   "Phone": "0901234567",
>   "Order_ID": "#TS9921",
>   "Product": "Chuột Logitech G102",
>   "Issue": "Bị double click",
>   "Status": "HỢP LỆ"
> }
> ```
> 
> *Ví dụ 2 (Dữ liệu thiếu/lỗi):*
> Input: "Tai nghe Sony bị rè quá shop ơi, đổi cho mình với. Mình tên Lan."
> Output:
> ```json
> {
>   "Customer_Name": "Lan",
>   "Phone": "NULL",
>   "Order_ID": "NULL",
>   "Product": "Tai nghe Sony",
>   "Issue": "Bị rè",
>   "Status": "CẦN XÁC MINH"
> }
> ```
> 
> **[T - Tone]** Khách quan, vô cảm như một cỗ máy máy chủ.
> 
> **[E - Extras]** 
> - **Negative Prompting:** KHÔNG viết lời chào dạo đầu, KHÔNG giải thích, KHÔNG thêm bất kỳ văn bản nào khác ngoài khối mã JSON.
> - **Định dạng:** Chỉ xuất duy nhất một khối mã JSON được bọc trong thẻ ` ```json ... ``` `. 
> 
> Bây giờ, hãy trích xuất dữ liệu từ Input sau:
> **Input:** "Mình mới nhận con bàn phím cơ Razer BlackWidow, cắm điện sáng đèn mà gõ phím cách không ăn. Kiểm tra bảo hành giúp mình. Mã đơn của mình là #TS4455. Liên hệ Hùng qua số 0988777666 nhé."

---

## 💻 KẾT QUẢ CHẠY THỬ TRÊN AI (MÔ PHỎNG OUTPUT)

*Khi nạp Prompt trên vào Trợ lý AI (ChatGPT/Gemini/Claude), do đã bị ép định dạng bằng Delimiters và Few-shot, AI sẽ hoàn toàn im lặng không chào hỏi và lập tức nhả ra kết quả sạch 100% như sau:*

```json
{
  "Customer_Name": "Hùng",
  "Phone": "0988777666",
  "Order_ID": "#TS4455",
  "Product": "Bàn phím cơ Razer BlackWidow",
  "Issue": "Sáng đèn nhưng gõ phím cách không ăn",
  "Status": "HỢP LỆ"
}