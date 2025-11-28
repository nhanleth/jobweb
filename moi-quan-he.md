# 🎓 HƯỚNG DẪN ĐẦY ĐỦ MỐI QUAN HỆ DATABASE JOBWEB

## 📚 MỤC LỤC

1. [Quan hệ One-to-One](#1-one-to-one-11---một-đối-một)
2. [Quan hệ One-to-Many](#2-one-to-many-1n---một-đối-nhiều)
3. [Quan hệ Many-to-Many](#3-many-to-many-nn---nhiều-đối-nhiều)
4. [Tổng hợp tất cả quan hệ](#4-tổng-hợp-tất-cả-quan-hệ)
5. [Truy vấn Laravel Eloquent](#5-truy-vấn-laravel-eloquent)

---

## 1. ONE-TO-ONE (1:1) - MỘT ĐỐI MỘT

### 📌 **A. users ↔ user_profiles**

**Giải thích:**

- 1 **user** (ứng viên) chỉ có 1 **hồ sơ cá nhân**
- 1 **hồ sơ** chỉ thuộc về 1 **user**

**Ví dụ thực tế:**

- Giống như 1 người chỉ có 1 CMND/CCCD
- 1 CMND/CCCD chỉ thuộc về 1 người

**Cách hoạt động:**

```
Bảng users:
| id | name      | email           | user_type |
|----|-----------|-----------------|-----------|
| 1  | Nguyễn A  | a@gmail.com     | user      |
| 2  | Trần B    | b@gmail.com     | user      |

Bảng user_profiles:
| id | user_id | avatar      | education      | skills              |
|----|---------|-------------|----------------|---------------------|
| 1  | 1       | avatar1.jpg | Đại học FPT    | PHP, Laravel, MySQL |
| 2  | 2       | avatar2.jpg | Đại học CNTT   | React, Node.js      |
     ↑
     Liên kết với user id
```

**Kịch bản sử dụng:**

```
1. User "Nguyễn A" đăng ký tài khoản
   └→ Tạo record trong bảng users (id=1)

2. User cập nhật hồ sơ cá nhân
   └→ Tạo record trong bảng user_profiles (user_id=1)

3. Xem hồ sơ của user
   └→ Query: user_profiles WHERE user_id = 1
```

**Laravel Model:**

```php
// User Model
public function profile() {
    return $this->hasOne(UserProfile::class);
}

// UserProfile Model
public function user() {
    return $this->belongsTo(User::class);
}

// Sử dụng:
$user = User::find(1);
$profile = $user->profile; // Lấy hồ sơ
echo $profile->education; // "Đại học FPT"
```

---

## 2. ONE-TO-MANY (1:N) - MỘT ĐỐI NHIỀU

### 📌 **A. users → applications**

**Giải thích:**

- 1 **user** có thể **ứng tuyển nhiều job_listings**
- 1 **đơn ứng tuyển** chỉ thuộc về 1 **user**

**Ví dụ thực tế:**

- 1 khách hàng có thể đặt nhiều đơn hàng
- 1 đơn hàng chỉ thuộc về 1 khách hàng

**Cách hoạt động:**

```
Bảng users:
| id | name      |
|----|-----------|
| 1  | Nguyễn A  |
| 2  | Trần B    |

Bảng applications:
| id | user_id | job_id | cv_file      | status   | applied_at          |
|----|---------|--------|--------------|----------|---------------------|
| 1  | 1       | 10     | cv_a_1.pdf   | pending  | 2025-01-15 10:00:00 |
| 2  | 1       | 15     | cv_a_2.pdf   | reviewed | 2025-01-16 14:30:00 |
| 3  | 1       | 20     | cv_a_3.pdf   | accepted | 2025-01-17 09:15:00 |
| 4  | 2       | 10     | cv_b_1.pdf   | pending  | 2025-01-18 11:00:00 |
     ↑         ↑
     User id=1 ứng tuyển 3 công việc khác nhau (job 10, 15, 20)
     job_id trỏ đến bảng job_listings
```

**Kịch bản sử dụng:**

```
1. User "Nguyễn A" tìm thấy job_listing "PHP Developer" (id=10)
2. User click "Ứng tuyển"
3. User upload CV và viết thư xin việc
4. Hệ thống tạo record trong bảng applications:
   └→ user_id = 1
   └→ job_id = 10 (trỏ đến job_listings)
   └→ cv_file = "cv_a_1.pdf"
   └→ status = "pending"

5. User tiếp tục ứng tuyển job khác (id=15, 20)
   └→ Tạo thêm 2 records nữa trong applications
```

**Laravel Model:**

```php
// User Model
public function applications() {
    return $this->hasMany(Application::class);
}

// Application Model
public function user() {
    return $this->belongsTo(User::class);
}
public function jobListing() {
    return $this->belongsTo(JobListing::class, 'job_id');
}

// Sử dụng:
$user = User::find(1);
$applications = $user->applications; // Lấy tất cả đơn ứng tuyển
echo $applications->count(); // 3 đơn
```

---

### 📌 **B. users → saved_jobs**

**Giải thích:**

- 1 **user** có thể **lưu nhiều job_listings yêu thích**
- 1 **saved_job** chỉ thuộc về 1 **user**

**Ví dụ thực tế:**

- 1 người có thể thêm nhiều sản phẩm vào giỏ hàng
- 1 sản phẩm trong giỏ chỉ thuộc về 1 người

**Cách hoạt động:**

```
Bảng users:
| id | name      |
|----|-----------|
| 1  | Nguyễn A  |

Bảng job_listings:
| id | title                  | company       |
|----|------------------------|---------------|
| 5  | PHP Developer          | FPT Software  |
| 12 | React Developer        | VNG Corp      |
| 25 | Business Analyst       | Viettel       |

Bảng saved_jobs:
| id | user_id | job_id | created_at          |
|----|---------|--------|---------------------|
| 1  | 1       | 5      | 2025-01-15 10:00:00 |
| 2  | 1       | 12     | 2025-01-16 14:30:00 |
| 3  | 1       | 25     | 2025-01-17 09:15:00 |
     ↑         ↑
     User id=1 đã lưu 3 công việc yêu thích
     job_id trỏ đến bảng job_listings
```

**Kịch bản sử dụng:**

```
1. User "Nguyễn A" đang tìm việc
2. User thấy job_listing "PHP Developer" hay → Click "Lưu"
   └→ Tạo record: saved_jobs (user_id=1, job_id=5)

3. User tiếp tục lưu thêm 2 jobs nữa
   └→ Tạo thêm 2 records trong saved_jobs

4. User vào mục "Việc đã lưu"
   └→ Query: saved_jobs WHERE user_id = 1
   └→ Hiển thị 3 jobs đã lưu

5. User bỏ lưu job id=12
   └→ Delete record: saved_jobs WHERE user_id=1 AND job_id=12
```

**Laravel Model:**

```php
// User Model
public function savedJobs() {
    return $this->hasMany(SavedJob::class);
}

// SavedJob Model
public function user() {
    return $this->belongsTo(User::class);
}
public function jobListing() {
    return $this->belongsTo(JobListing::class, 'job_id');
}

// Sử dụng:
$user = User::find(1);
$savedJobs = $user->savedJobs()->with('jobListing')->get();
foreach($savedJobs as $saved) {
    echo $saved->jobListing->title; // PHP Developer, React Developer...
}
```

---

### 📌 **C. users → notifications**

**Giải thích:**

- 1 **user** có thể nhận **nhiều thông báo**
- 1 **thông báo** chỉ gửi cho 1 **user**

**Ví dụ thực tế:**

- 1 người có thể nhận nhiều tin nhắn
- 1 tin nhắn chỉ gửi cho 1 người cụ thể

**Cách hoạt động:**

```
Bảng users:
| id | name      | user_type |
|----|-----------|-----------|
| 1  | Nguyễn A  | user      |
| 5  | HR Lan    | company   |

Bảng notifications:
| id | user_id | title                    | message                          | type        | is_read |
|----|---------|--------------------------|----------------------------------|-------------|---------|
| 1  | 1       | Đơn đã được xem          | FPT Software đã xem CV của bạn   | application | false   |
| 2  | 1       | Việc làm phù hợp         | 5 việc mới phù hợp với bạn       | job         | false   |
| 3  | 1       | Chúc mừng!               | Bạn đã được chấp nhận            | application | false   |
| 4  | 5       | Có ứng viên mới          | Nguyễn A đã ứng tuyển            | application | false   |
     ↑                                                                       ↑
     User id=1 có 3 thông báo                                      User id=5 (HR) có 1 thông báo
```

**Kịch bản sử dụng:**

```
1. User "Nguyễn A" ứng tuyển job_listing của FPT
   └→ Hệ thống tạo thông báo cho HR của FPT:
      notifications (user_id=5, message="Nguyễn A đã ứng tuyển")

2. HR xem CV của user
   └→ Hệ thống tạo thông báo cho user:
      notifications (user_id=1, message="FPT đã xem CV của bạn")

3. HR chấp nhận đơn
   └→ Hệ thống tạo thông báo cho user:
      notifications (user_id=1, message="Bạn đã được chấp nhận")

4. User vào mục "Thông báo"
   └→ Query: notifications WHERE user_id = 1 ORDER BY created_at DESC
   └→ Hiển thị 3 thông báo chưa đọc
```

**Laravel Model:**

```php
// User Model
public function notifications() {
    return $this->hasMany(Notification::class);
}

// Notification Model
public function user() {
    return $this->belongsTo(User::class);
}

// Sử dụng:
$user = User::find(1);
$unreadNotifications = $user->notifications()
    ->where('is_read', false)
    ->orderBy('created_at', 'desc')
    ->get();
echo $unreadNotifications->count(); // 3 thông báo chưa đọc
```

---

### 📌 **D. job_listings → applications** ⭐

**Giải thích:**

- 1 **job_listing** có thể nhận **nhiều đơn ứng tuyển**
- 1 **đơn ứng tuyển** chỉ cho 1 **job_listing**

**Ví dụ thực tế:**

- 1 sản phẩm có thể có nhiều đánh giá
- 1 đánh giá chỉ cho 1 sản phẩm

**Cách hoạt động:**

```
Bảng job_listings:
| id | title                  | company       |
|----|------------------------|---------------|
| 10 | PHP Developer          | FPT Software  |
| 15 | React Developer        | VNG Corp      |

Bảng applications:
| id | user_id | job_id | cv_file      | status   | applied_at          |
|----|---------|--------|--------------|----------|---------------------|
| 1  | 1       | 10     | cv_a_1.pdf   | pending  | 2025-01-15 10:00:00 |
| 2  | 5       | 10     | cv_b_1.pdf   | reviewed | 2025-01-16 14:30:00 |
| 3  | 8       | 10     | cv_c_1.pdf   | accepted | 2025-01-17 09:15:00 |
| 4  | 1       | 15     | cv_a_2.pdf   | pending  | 2025-01-18 11:00:00 |
              ↑
              job_id trỏ đến bảng job_listings
              Job id=10 có 3 người ứng tuyển (user 1, 5, 8)
```

**Kịch bản sử dụng:**

```
1. FPT đăng tin tuyển "PHP Developer" (job_id=10 trong job_listings)
2. 3 ứng viên ứng tuyển vào job này:
   └→ Nguyễn A (user_id=1) ứng tuyển
   └→ Trần B (user_id=5) ứng tuyển
   └→ Lê C (user_id=8) ứng tuyển
   └→ Tạo 3 records trong applications (cùng job_id=10)

3. HR của FPT vào xem danh sách ứng viên:
   └→ Query: applications WHERE job_id = 10
   └→ Hiển thị 3 ứng viên đã apply

4. HR duyệt từng CV:
   └→ Xem CV của user 1 → Update status = "reviewed"
   └→ Chấp nhận user 8 → Update status = "accepted"
```

**Laravel Model:**

```php
// JobListing Model
public function applications() {
    return $this->hasMany(Application::class, 'job_id');
}

// Application Model
public function jobListing() {
    return $this->belongsTo(JobListing::class, 'job_id');
}

// Sử dụng:
$jobListing = JobListing::find(10);
$applications = $jobListing->applications()->with('user')->get();
echo "Có {$applications->count()} người ứng tuyển"; // 3 người

foreach($applications as $app) {
    echo $app->user->name; // Nguyễn A, Trần B, Lê C
}
```

---

## 3. MANY-TO-MANY (N:N) - NHIỀU ĐỐI NHIỀU

### 📌 **A. companies ↔ job_listings** (qua bảng company_job) ⭐

**Giải thích:**

- 1 **company** có thể đăng **nhiều job_listings**
- 1 **job_listing** có thể được đăng bởi **nhiều companies** (trường hợp liên kết tuyển dụng)

**Ví dụ thực tế:**

- 1 sinh viên học nhiều môn học
- 1 môn học có nhiều sinh viên

**❗ Cần bảng trung gian: company_job**

**Cách hoạt động:**

```
Bảng companies:
| id | name              | type        |
|----|-------------------|-------------|
| 1  | FPT Software      | Cổ phần     |
| 2  | VNG Corporation   | TNHH        |
| 3  | FPT Telecom       | Cổ phần     |

Bảng job_listings:
| id | title                 | salary      | deadline   |
|----|-----------------------|-------------|------------|
| 10 | PHP Developer         | 15-20 triệu | 2025-02-28 |
| 15 | React Developer       | 20-25 triệu | 2025-03-15 |
| 20 | Business Analyst      | 12-18 triệu | 2025-03-01 |

Bảng company_job (Bảng trung gian):
| id | company_id | job_id | created_at          |
|----|------------|--------|---------------------|
| 1  | 1          | 10     | 2025-01-15 10:00:00 | ← FPT Software đăng PHP Developer
| 2  | 1          | 15     | 2025-01-16 14:00:00 | ← FPT Software đăng React Developer
| 3  | 2          | 10     | 2025-01-17 09:00:00 | ← VNG cũng đăng PHP Developer (liên kết)
| 4  | 3          | 20     | 2025-01-18 11:00:00 | ← FPT Telecom đăng Business Analyst
              ↑
              job_id trỏ đến bảng job_listings
```

**Phân tích:**

```
Company id=1 (FPT Software) có:
  └→ Job 10 (PHP Developer)
  └→ Job 15 (React Developer)

Company id=2 (VNG) có:
  └→ Job 10 (PHP Developer) [cùng job với FPT]

Company id=3 (FPT Telecom) có:
  └→ Job 20 (Business Analyst)

Job id=10 (PHP Developer) được đăng bởi:
  └→ FPT Software
  └→ VNG Corporation
```

**Kịch bản sử dụng:**

```
CASE 1: Công ty đăng tin thông thường
1. HR của FPT Software đăng tin "PHP Developer"
   └→ Tạo record trong bảng job_listings (id=10)
   └→ Tạo record trong bảng company_job:
      company_id = 1
      job_id = 10

2. User tìm kiếm "PHP Developer"
   └→ Thấy job id=10
   └→ Hiển thị: "FPT Software tuyển PHP Developer"

CASE 2: Liên kết tuyển dụng
1. VNG muốn tuyển cùng vị trí với FPT
2. VNG liên kết với job id=10 (đã có sẵn)
   └→ Tạo thêm record trong company_job:
      company_id = 2
      job_id = 10

3. User tìm kiếm "PHP Developer"
   └→ Thấy job id=10
   └→ Hiển thị: "FPT Software, VNG tuyển PHP Developer"
```

**Laravel Model:**

```php
// Company Model
public function jobListings() {
    return $this->belongsToMany(JobListing::class, 'company_job', 'company_id', 'job_id')
                ->withTimestamps();
}

// JobListing Model
public function companies() {
    return $this->belongsToMany(Company::class, 'company_job', 'job_id', 'company_id')
                ->withTimestamps();
}

// Sử dụng:
// Lấy tất cả job_listings của FPT Software
$company = Company::find(1);
$jobListings = $company->jobListings;
echo $jobListings->count(); // 2 jobs (PHP Developer, React Developer)

// Lấy tất cả companies đăng job_listing "PHP Developer"
$jobListing = JobListing::find(10);
$companies = $jobListing->companies;
echo $companies->count(); // 2 companies (FPT, VNG)
```

---

### 📌 **B. categories ↔ job_listings** (qua bảng category_job) ⭐

**Giải thích:**

- 1 **category** có thể chứa **nhiều job_listings**
- 1 **job_listing** có thể thuộc **nhiều categories**

**Ví dụ thực tế:**

- 1 hashtag có nhiều bài viết
- 1 bài viết có nhiều hashtags

**❗ Cần bảng trung gian: category_job**

**Cách hoạt động:**

```
Bảng categories:
| id | name                | icon           |
|----|---------------------|----------------|
| 1  | IT - Phần mềm       | 💻             |
| 2  | Fullstack           | 🔄             |
| 3  | Backend             | ⚙️              |
| 4  | PHP                 | 🐘             |

Bảng job_listings:
| id | title                 | salary      |
|----|-----------------------|-------------|
| 10 | PHP Developer         | 15-20 triệu |
| 15 | React Developer       | 20-25 triệu |
| 20 | Java Backend          | 18-25 triệu |

Bảng category_job (Bảng trung gian):
| id | category_id | job_id | created_at          |
|----|-------------|--------|---------------------|
| 1  | 1           | 10     | 2025-01-15 10:00:00 | ← PHP Dev thuộc "IT - Phần mềm"
| 2  | 2           | 10     | 2025-01-15 10:00:00 | ← PHP Dev thuộc "Fullstack"
| 3  | 3           | 10     | 2025-01-15 10:00:00 | ← PHP Dev thuộc "Backend"
| 4  | 4           | 10     | 2025-01-15 10:00:00 | ← PHP Dev thuộc "PHP"
| 5  | 1           | 15     | 2025-01-16 14:00:00 | ← React Dev thuộc "IT - Phần mềm"
| 6  | 2           | 15     | 2025-01-16 14:00:00 | ← React Dev thuộc "Fullstack"
| 7  | 1           | 20     | 2025-01-17 09:00:00 | ← Java Backend thuộc "IT - Phần mềm"
| 8  | 3           | 20     | 2025-01-17 09:00:00 | ← Java Backend thuộc "Backend"
              ↑
              job_id trỏ đến bảng job_listings
```

**Phân tích:**

```
Job id=10 (PHP Developer) thuộc 4 categories:
  └→ IT - Phần mềm
  └→ Fullstack
  └→ Backend
  └→ PHP

Job id=15 (React Developer) thuộc 2 categories:
  └→ IT - Phần mềm
  └→ Fullstack

Job id=20 (Java Backend) thuộc 2 categories:
  └→ IT - Phần mềm
  └→ Backend

Category id=1 (IT - Phần mềm) chứa 3 job_listings:
  └→ PHP Developer
  └→ React Developer
  └→ Java Backend

Category id=2 (Fullstack) chứa 2 job_listings:
  └→ PHP Developer
  └→ React Developer
```

**Kịch bản sử dụng:**

```
CASE 1: Company đăng tin tuyển dụng
1. HR đăng tin "PHP Developer" trong job_listings
2. Chọn categories phù hợp:
   ☑ IT - Phần mềm
   ☑ Fullstack
   ☑ Backend
   ☑ PHP
3. Hệ thống tạo:
   └→ 1 record trong job_listings (id=10)
   └→ 4 records trong category_job (liên kết với 4 categories)

CASE 2: User lọc việc làm theo category
1. User click category "Backend"
   └→ Query: category_job WHERE category_id = 3
   └→ Lấy được job_id: 10, 20
   └→ Hiển thị 2 job_listings: "PHP Developer", "Java Backend"

2. User click category "PHP"
   └→ Query: category_job WHERE category_id = 4
   └→ Lấy được job_id: 10
   └→ Hiển thị 1 job_listing: "PHP Developer"

CASE 3: Hiển thị badges cho job_listing
1. Hiển thị job_listing "PHP Developer"
2. Lấy tất cả categories:
   └→ Query: category_job WHERE job_id = 10
   └→ Hiển thị badges: [IT - Phần mềm] [Fullstack] [Backend] [PHP]
```

**Laravel Model:**

```php
// Category Model
public function jobListings() {
    return $this->belongsToMany(JobListing::class, 'category_job', 'category_id', 'job_id')
                ->withTimestamps();
}

// JobListing Model
public function categories() {
    return $this->belongsToMany(Category::class, 'category_job', 'job_id', 'category_id')
                ->withTimestamps();
}

// Sử dụng:
// Lấy tất cả job_listings trong category "Backend"
$category = Category::find(3);
$jobListings = $category->jobListings;
echo $jobListings->count(); // 2 jobs (PHP Developer, Java Backend)

// Lấy tất cả categories của job_listing "PHP Developer"
$jobListing = JobListing::find(10);
$categories = $jobListing->categories;
foreach($categories as $cat) {
    echo $cat->name; // IT - Phần mềm, Fullstack, Backend, PHP
}
```

---

### 📌 **C. users (company) ↔ companies** (qua bảng user_company)

**Giải thích:**

- 1 **user company** có thể quản lý **nhiều companies**
- 1 **company** có thể có **nhiều users quản lý**

**Ví dụ thực tế:**

- 1 giáo viên dạy nhiều lớp
- 1 lớp có nhiều giáo viên

**❗ Cần bảng trung gian: user_company**

**Cách hoạt động:**

```
Bảng users:
| id | name         | email              | user_type |
|----|--------------|--------------------|-----------|
| 5  | HR Lan       | lan@fpt.com        | company   |
| 6  | CEO Minh     | minh@fpt.com       | company   |
| 7  | HR Hương     | huong@vng.com      | company   |

Bảng companies:
| id | name              | type        |
|----|-------------------|-------------|
| 1  | FPT Software      | Cổ phần     |
| 2  | FPT Telecom       | Cổ phần     |
| 3  | VNG Corporation   | TNHH        |

Bảng user_company (Bảng trung gian):
| id | user_id | company_id | created_at          |
|----|---------|------------|---------------------|
| 1  | 5       | 1          | 2025-01-15 10:00:00 | ← HR Lan quản lý FPT Software
| 2  | 5       | 2          | 2025-01-15 10:00:00 | ← HR Lan quản lý FPT Telecom
| 3  | 6       | 1          | 2025-01-16 14:00:00 | ← CEO Minh quản lý FPT Software
| 4  | 7       | 3          | 2025-01-17 09:00:00 | ← HR Hương quản lý VNG
```

**Phân tích:**

```
User id=5 (HR Lan) quản lý 2 companies:
  └→ FPT Software
  └→ FPT Telecom

User id=6 (CEO Minh) quản lý 1 company:
  └→ FPT Software

User id=7 (HR Hương) quản lý 1 company:
  └→ VNG Corporation

Company id=1 (FPT Software) có 2 người quản lý:
  └→ HR Lan
  └→ CEO Minh

Company id=2 (FPT Telecom) có 1 người quản lý:
  └→ HR Lan

Company id=3 (VNG) có 1 người quản lý:
  └→ HR Hương
```

**Kịch bản sử dụng:**

```
CASE 1: User company đăng nhập
1. HR Lan đăng nhập (user_id=5)
2. Hệ thống check: user_company WHERE user_id = 5
   └→ Lấy được company_id: 1, 2
   └→ Hiển thị dropdown chọn công ty:
      [FPT Software] [FPT Telecom]

3. HR Lan chọn "FPT Software"
   └→ Session lưu: current_company_id = 1
   └→ Tất cả job_listings hiển thị là của FPT Software

CASE 2: Thêm người quản lý cho company
1. Admin thêm "CEO Minh" vào quản lý FPT Software
   └→ Insert record vào user_company:
      user_id = 6
      company_id = 1

2. CEO Minh đăng nhập
   └→ Thấy company "FPT Software" trong danh sách
   └→ Có thể quản lý job_listings của FPT Software

CASE 3: HR đăng tin tuyển dụng
1. HR Lan chọn company "FPT Software"
2. HR đăng tin "PHP Developer"
   └→ Tạo record trong job_listings (id=10)
   └→ Tạo record trong company_job:
      company_id = 1 (FPT Software)
      job_id = 10
```

**Laravel Model:**

```php
// User Model
public function companies() {
    return $this->belongsToMany(Company::class, 'user_company')
                ->withTimestamps();
}

// Company Model
public function users() {
    return $this->belongsToMany(User::class, 'user_company')
                ->withTimestamps();
}

// Sử dụng:
// Lấy tất cả companies của HR Lan
$user = User::find(5);
$companies = $user->companies;
echo $companies->count(); // 2 companies (FPT Software, FPT Telecom)

// Lấy tất cả users quản lý FPT Software
$company = Company::find(1);
$managers = $company->users;
echo $managers->count(); // 2 người (HR Lan, CEO Minh)
```

---

## 4. TỔNG HỢP TẤT CẢ QUAN HỆ

### 📊 **Bảng tổng hợp**

#### **One-to-One (1:1)**

| Bảng cha | Bảng con        | Giải thích                | Foreign Key |
| -------- | --------------- | ------------------------- | ----------- |
| `users`  | `user_profiles` | 1 user có 1 hồ sơ cá nhân | `user_id`   |

#### **One-to-Many (1:N)**

| Bảng cha       | Bảng con        | Giải thích                           | Foreign Key |
| -------------- | --------------- | ------------------------------------ | ----------- |
| `users`        | `applications`  | 1 user ứng tuyển nhiều job_listings  | `user_id`   |
| `users`        | `saved_jobs`    | 1 user lưu nhiều job_listings        | `user_id`   |
| `users`        | `notifications` | 1 user nhận nhiều thông báo          | `user_id`   |
| `job_listings` | `applications`  | 1 job_listing có nhiều đơn ứng tuyển | `job_id`    |

#### **Many-to-Many (N:N)**

| Bảng 1       | Bảng trung gian | Bảng 2         | Giải thích                                                  |
| ------------ | --------------- | -------------- | ----------------------------------------------------------- |
| `companies`  | `company_job`   | `job_listings` | 1 company đăng nhiều jobs, 1 job có thể thuộc nhiều company |
| `categories` | `category_job`  | `job_listings` | 1 category chứa nhiều jobs, 1 job thuộc nhiều categories    |
| `users`      | `user_company`  | `companies`    | 1 user quản lý nhiều companies, 1 company có nhiều quản lý  |

---

### 📈 **Sơ đồ quan hệ tổng thể**

```
┌─────────────┐
│   users     │
└──────┬──────┘
       │
       ├─ 1:1 ────► user_profiles
       │
       ├─ 1:N ────► applications ◄──── N:1 ──── job_listings
       │
       ├─ 1:N ────► saved_jobs ◄────────────── job_listings
       │
       ├─ 1:N ────► notifications
       │
       └─ N:N ────► user_company ◄──── N:N ──── companies
                                                      │
                                                      │
                                                      └─ N:N ──► company_job ◄─ N:N ── job_listings
                                                                                            │
                                                                                            │
                                                                                            └─ N:N ──► category_job ◄─ N:N ── categories
```

---

## 5. TRUY VẤN LARAVEL ELOQUENT

### 🔍 **A. Truy vấn One-to-One**

```php
// Lấy user và hồ sơ của user
$user = User::with('profile')->find(1);
echo $user->profile->education;

// Lấy profile và user của profile
$profile = UserProfile::with('user')->find(1);
echo $profile->user->name;
```

---

### 🔍 **B. Truy vấn One-to-Many**

```php
// 1. User → Applications
$user = User::with('applications')->find(1);
foreach($user->applications as $app) {
    echo $app->jobListing->title;
}

// 2. User → Saved Jobs
$user = User::with(['savedJobs.jobListing'])->find(1);
foreach($user->savedJobs as $saved) {
    echo $saved->jobListing->title;
}

// 3. User → Notifications
$user = User::find(1);
$unreadNotifications = $user->notifications()
    ->where('is_read', false)
    ->latest()
    ->get();

// 4. JobListing → Applications
$jobListing = JobListing::with(['applications.user'])->find(10);
echo "Có {$jobListing->applications->count()} người ứng tuyển";
foreach($jobListing->applications as $app) {
    echo $app->user->name;
}
```

---

### 🔍 **C. Truy vấn Many-to-Many**

```php
// 1. Companies ↔ JobListings
// Lấy tất cả job_listings của 1 company
$company = Company::with('jobListings')->find(1);
foreach($company->jobListings as $job) {
    echo $job->title;
}

// Lấy tất cả companies của 1 job_listing
$jobListing = JobListing::with('companies')->find(10);
foreach($jobListing->companies as $company) {
    echo $company->name;
}

// 2. Categories ↔ JobListings
// Lấy tất cả job_listings trong 1 category
$category = Category::with('jobListings')->find(3);
foreach($category->jobListings as $job) {
    echo $job->title;
}

// Lấy tất cả categories của 1 job_listing
$jobListing = JobListing::with('categories')->find(10);
foreach($jobListing->categories as $cat) {
    echo $cat->name;
}

// 3. Users ↔ Companies
// Lấy tất cả companies của 1 user
$user = User::with('companies')->find(5);
foreach($user->companies as $company) {
    echo $company->name;
}

// Lấy tất cả users quản lý 1 company
$company = Company::with('users')->find(1);
foreach($company->users as $user) {
    echo $user->name;
}
```

---

### 🔍 **D. Truy vấn phức tạp (Nested Relationships)**

```php
// 1. Lấy user với tất cả applications và job_listings tương ứng
$user = User::with(['applications.jobListing.companies'])->find(1);
foreach($user->applications as $app) {
    echo "Ứng tuyển: " . $app->jobListing->title;
    echo " tại " . $app->jobListing->companies->first()->name;
}

// 2. Lấy company với tất cả job_listings và categories
$company = Company::with(['jobListings.categories'])->find(1);
foreach($company->jobListings as $job) {
    echo $job->title . " - Categories: ";
    echo $job->categories->pluck('name')->join(', ');
}

// 3. Lấy job_listing với tất cả thông tin liên quan
$jobListing = JobListing::with([
    'companies',
    'categories',
    'applications.user.profile'
])->find(10);

echo "Job: " . $jobListing->title;
echo "Companies: " . $jobListing->companies->pluck('name')->join(', ');
echo "Categories: " . $jobListing->categories->pluck('name')->join(', ');
echo "Số ứng viên: " . $jobListing->applications->count();
```

---

### 🔍 **E. Truy vấn có điều kiện (Query Constraints)**

```php
// 1. Lấy user với các applications có status = 'pending'
$user = User::with(['applications' => function($query) {
    $query->where('status', 'pending');
}])->find(1);

// 2. Lấy job_listings có deadline chưa hết hạn
$activeJobs = JobListing::where('deadline', '>=', now())
    ->where('status', 'active')
    ->where('approval_status', 'approved')
    ->with(['companies', 'categories'])
    ->get();

// 3. Lấy company với job_listings đang active
$company = Company::with(['jobListings' => function($query) {
    $query->where('status', 'active')
          ->where('approval_status', 'approved')
          ->where('deadline', '>=', now());
}])->find(1);

// 4. Lấy category với số lượng job_listings
$categories = Category::withCount('jobListings')
    ->having('job_listings_count', '>', 0)
    ->get();
```

---

### 🔍 **F. Attach/Detach/Sync cho Many-to-Many**

```php
// 1. Company đăng job_listing mới
$company = Company::find(1);
$jobListing = JobListing::create([...]);
$company->jobListings()->attach($jobListing->id);

// 2. Thêm categories cho job_listing
$jobListing = JobListing::find(10);
$jobListing->categories()->attach([1, 2, 3, 4]); // IT, Fullstack, Backend, PHP

// 3. Xóa category khỏi job_listing
$jobListing->categories()->detach(4); // Xóa PHP

// 4. Sync categories (xóa cũ, thêm mới)
$jobListing->categories()->sync([1, 2, 3]); // Chỉ giữ lại 3 categories

// 5. User lưu job_listing
$user = User::find(1);
$user->savedJobs()->create(['job_id' => 10]);

// 6. User bỏ lưu job_listing
$user->savedJobs()->where('job_id', 10)->delete();
```

---

### 🔍 **G. Truy vấn thống kê**

```php
// 1. Đếm số applications của user
$user = User::withCount('applications')->find(1);
echo $user->applications_count;

// 2. Đếm số job_listings của company
$company = Company::withCount('jobListings')->find(1);
echo $company->job_listings_count;

// 3. Lấy job_listings có nhiều applications nhất
$popularJobs = JobListing::withCount('applications')
    ->orderBy('applications_count', 'desc')
    ->take(10)
    ->get();

// 4. Lấy categories có nhiều job_listings nhất
$popularCategories = Category::withCount('jobListings')
    ->orderBy('job_listings_count', 'desc')
    ->get();

// 5. Thống kê theo status
$stats = Application::selectRaw('status, COUNT(*) as count')
    ->groupBy('status')
    ->get();
```

---

## 🎯 TÓM TẮT QUAN TRỌNG

### ✅ **Các điểm cần nhớ:**

1. **Tên bảng:**

   - ✅ Bảng tin tuyển dụng: `job_listings` (KHÔNG phải `jobs`)
   - ✅ Foreign key: `job_id` trỏ đến bảng `job_listings`

2. **One-to-One:**

   - `users` ↔ `user_profiles`
   - Dùng: `hasOne()` và `belongsTo()`

3. **One-to-Many:**

   - `users` → `applications`, `saved_jobs`, `notifications`
   - `job_listings` → `applications`
   - Dùng: `hasMany()` và `belongsTo()`

4. **Many-to-Many:**

   - `companies` ↔ `job_listings` (qua `company_job`)
   - `categories` ↔ `job_listings` (qua `category_job`)
   - `users` ↔ `companies` (qua `user_company`)
   - Dùng: `belongsToMany()`

5. **Bảng trung gian:**
   - `company_job`
   - `category_job`
   - `user_company`

---

## 🎉 HOÀN TẤT!

Bây giờ bạn đã hiểu đầy đủ về mối quan hệ giữa các bảng trong database JobWeb! 🚀

**Bước tiếp theo:** Tạo Models và định nghĩa Relationships trong Laravel!
