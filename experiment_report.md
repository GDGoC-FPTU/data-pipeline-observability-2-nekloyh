# Experiment Report: Data Quality Impact on AI Agent

**Student ID:** AI20K-2A202600782
**Name:** Nguyen Ly Minh Ky
**Date:** 10-06-2026

---

## 1. Thiet lap thi nghiem

Thi nghiem duoc chay lai tu dau theo thu tu:

```bash
uv run python generate_garbage.py
uv run python solution.py
uv run python agent_simulation.py
uv run python -m pytest -v
```

Truy van dung cho ca hai kich ban la: `What is the best electronic product?`.
Agent loc cac dong co `category = electronics`, sau do chon san pham co `price`
lon nhat. Diem accuracy danh gia do dung va huu ich cua cau tra loi doi voi nguoi
dung, khong chi danh gia viec chuong trinh co chay dung ham `idxmax` hay khong.

## 2. Ket qua thi nghiem

ETL doc 5 records tu `raw_data.json`, giu lai 3 records hop le va loai 2 records
khong dat quy tac validation. Trong clean data co 2 san pham electronics:
Laptop ($1200) va Monitor ($300).

| Scenario | Agent Response | Accuracy (1-10) | Notes |
|---|---|---:|---|
| Clean Data (`processed_data.csv`) | `Agent: Based on my data, the best choice is Laptop at $1200.` | 10 | Dung voi logic thi nghiem: Laptop la san pham electronics co gia cao nhat trong du lieu da validate. |
| Garbage Data (`garbage_data.csv`) | `Agent: Based on my data, the best choice is Nuclear Reactor at $999999.` | 1 | Agent bi outlier chi phoi va dua ra mot khuyen nghi khong hop ly, khong huu ich cho nguoi dung. |

---

## 3. Phan tich va nhan xet

### Tai sao Agent tra loi sai khi dung Garbage Data?

`garbage_data.csv` co 5 records nhung chua nhieu loi chat luong. ID `1` xuat
hien hai lan, lam mat tinh duy nhat cua khoa va co the gay nham lan khi truy vet
hoac join du lieu. Gia cua `Broken Chair` la chuoi `ten dollars` thay vi so; neu
record nay nam trong category dang truy van, phep tim gia lon nhat co the cho ket
qua sai hoac phat sinh loi kieu du lieu. `Ghost Item` thieu ID, thieu category va
co gia bang 0, nen khong du thong tin de tim kiem hay khuyen nghi tin cay.

Tac dong truc tiep lon nhat den lan chay nay la outlier `Nuclear Reactor` co gia
$999999. Gia nay cao gap khoang 833.33 lan Laptop ($1200). Do agent chi dung
`price` lon nhat lam tieu chi "best", outlier hop le ve mat cu phap va co category
`electronics` se luon thang. Agent khong co validation, rang buoc mien gia tri,
kiem tra outlier hay ngu canh san pham, vi vay no bien du lieu bi dau doc thanh
mot cau tra loi tu tin nhung vo ly.

Khong phai moi loi deu truc tiep thay doi cau tra loi trong truy van cu the nay:
duplicate ID nam o Laptop/Banana, wrong type nam trong furniture, con null
category bi bo qua khi loc electronics. Tuy nhien, chung van la rui ro he thong
va co the lam hong cac truy van khac. Ket qua cho thay output dung theo code
khong dong nghia voi thong tin dung trong thuc te.

---

## 4. Ket luan

**Quality Data > Quality Prompt? Dong y.**

Toi hoan toan dong y.
Prompt tot co the mo ta muc tieu ro hon, nhung khong the tu sua duplicate,
wrong type, null hay outlier khong duoc danh dau trong nguon du lieu. Voi cung
mot prompt va cung mot logic agent, clean data tao ra khuyen nghi hop ly trong
khi garbage data tao ra khuyen nghi sai lech. Can validate schema, ep kieu gia,
kiem tra ID duy nhat, loai null va phat hien outlier truoc khi du lieu duoc dua
vao agent. Prompt va guardrail van can thiet, nhung data quality la nen tang de
cau tra loi co the tin cay.
