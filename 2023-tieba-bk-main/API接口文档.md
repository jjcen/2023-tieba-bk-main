# 百度贴吧项目API接口文档

## 1. API基础信息

### 1.1 基础URL
```
开发环境: http://localhost:8000/api/
生产环境: https://api.tieba.com/api/
```

### 1.2 请求格式
- **Content-Type**: `application/json`
- **字符编码**: UTF-8

### 1.3 响应格式
```json
{
    "code": 200,
    "message": "success",
    "data": {},
    "timestamp": "2023-12-01T10:00:00Z"
}
```

### 1.4 状态码说明
| 状态码 | 说明 |
|--------|------|
| 200 | 请求成功 |
| 400 | 请求参数错误 |
| 401 | 未授权 |
| 403 | 禁止访问 |
| 404 | 资源不存在 |
| 500 | 服务器内部错误 |

## 2. 认证授权接口

### 2.1 用户注册

**接口地址**: `POST /auth/register/`

**请求参数**:
```json
{
    "username": "testuser",
    "email": "test@example.com",
    "phone": "13800138000",
    "password": "password123",
    "nickname": "测试用户",
    "captcha": "123456"
}
```

**响应示例**:
```json
{
    "code": 200,
    "message": "注册成功",
    "data": {
        "user_id": 1,
        "username": "testuser",
        "nickname": "测试用户",
        "email": "test@example.com"
    }
}
```

### 2.2 用户登录

**接口地址**: `POST /auth/login/`

**请求参数**:
```json
{
    "login_type": "password", // password, sms, wechat
    "username": "testuser",
    "password": "password123",
    "captcha": "123456"
}
```

**响应示例**:
```json
{
    "code": 200,
    "message": "登录成功",
    "data": {
        "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
        "user": {
            "id": 1,
            "username": "testuser",
            "nickname": "测试用户",
            "avatar": "/static/avatars/default.png"
        }
    }
}
```

### 2.3 获取用户信息

**接口地址**: `GET /auth/profile/`

**请求头**:
```
Authorization: Bearer {token}
```

**响应示例**:
```json
{
    "code": 200,
    "message": "success",
    "data": {
        "user": {
            "id": 1,
            "username": "testuser",
            "nickname": "测试用户",
            "email": "test@example.com",
            "phone": "13800138000",
            "avatar": "/static/avatars/default.png",
            "gender": 0,
            "birthday": "1990-01-01",
            "location": "北京",
            "signature": "这个人很懒，什么都没有留下",
            "created_at": "2023-01-01T00:00:00Z"
        },
        "profile": {
            "post_count": 10,
            "comment_count": 50,
            "like_count": 100,
            "collect_count": 20,
            "follow_count": 5,
            "follower_count": 15,
            "experience": 1000,
            "level": 3
        }
    }
}
```

## 3. 贴吧管理接口

### 3.1 获取贴吧列表

**接口地址**: `GET /tieba/`

**查询参数**:
- `page`: 页码 (默认: 1)
- `page_size`: 每页数量 (默认: 20)
- `category_id`: 分类ID
- `sort`: 排序方式 (hot, new, member)
- `keyword`: 搜索关键词

**响应示例**:
```json
{
    "code": 200,
    "message": "success",
    "data": {
        "tieba_list": [
            {
                "id": 1,
                "name": "python",
                "description": "Python编程语言讨论",
                "avatar": "/static/tieba/python.png",
                "member_count": 10000,
                "post_count": 5000,
                "today_post_count": 50,
                "owner": {
                    "id": 1,
                    "nickname": "管理员"
                },
                "is_member": true,
                "member_role": 0
            }
        ],
        "pagination": {
            "page": 1,
            "page_size": 20,
            "total": 100,
            "total_pages": 5
        }
    }
}
```

### 3.2 创建贴吧

**接口地址**: `POST /tieba/`

**请求头**:
```
Authorization: Bearer {token}
```

**请求参数**:
```json
{
    "name": "new_tieba",
    "description": "新贴吧描述",
    "category_id": 1,
    "privacy": 0,
    "avatar": "文件对象"
}
```

### 3.3 获取贴吧详情

**接口地址**: `GET /tieba/{tieba_id}/`

**响应示例**:
```json
{
    "code": 200,
    "message": "success",
    "data": {
        "tieba": {
            "id": 1,
            "name": "python",
            "description": "Python编程语言讨论",
            "avatar": "/static/tieba/python.png",
            "banner": "/static/tieba/banner.png",
            "member_count": 10000,
            "post_count": 5000,
            "today_post_count": 50,
            "owner": {
                "id": 1,
                "nickname": "管理员",
                "avatar": "/static/avatars/admin.png"
            },
            "category": {
                "id": 1,
                "name": "编程"
            },
            "announcements": [
                {
                    "id": 1,
                    "title": "贴吧公告",
                    "content": "欢迎来到Python贴吧",
                    "author": {
                        "id": 1,
                        "nickname": "管理员"
                    },
                    "created_at": "2023-01-01T00:00:00Z"
                }
            ],
            "managers": [
                {
                    "user_id": 2,
                    "nickname": "小吧主",
                    "role": 1
                }
            ]
        },
        "user_status": {
            "is_member": true,
            "member_role": 0,
            "join_date": "2023-01-01"
        }
    }
}
```

## 4. 帖子管理接口

### 4.1 获取帖子列表

**接口地址**: `GET /posts/`

**查询参数**:
- `tieba_id`: 贴吧ID
- `page`: 页码
- `page_size`: 每页数量
- `sort`: 排序方式 (new, hot, essence)
- `author_id`: 作者ID
- `keyword`: 搜索关键词

**响应示例**:
```json
{
    "code": 200,
    "message": "success",
    "data": {
        "posts": [
            {
                "id": 1,
                "title": "Python学习路线",
                "author": {
                    "id": 1,
                    "nickname": "Python爱好者",
                    "avatar": "/static/avatars/user1.png"
                },
                "tieba": {
                    "id": 1,
                    "name": "python"
                },
                "view_count": 1000,
                "reply_count": 50,
                "like_count": 100,
                "is_top": false,
                "is_essence": true,
                "created_at": "2023-01-01T10:00:00Z",
                "last_reply_at": "2023-01-02T15:30:00Z",
                "preview_content": "本文介绍Python学习路线...",
                "image_count": 3
            }
        ],
        "pagination": {
            "page": 1,
            "page_size": 20,
            "total": 100,
            "total_pages": 5
        }
    }
}
```

### 4.2 创建帖子

**接口地址**: `POST /posts/`

**请求头**:
```
Authorization: Bearer {token}
Content-Type: multipart/form-data
```

**请求参数**:
```json
{
    "tieba_id": 1,
    "title": "帖子标题",
    "content": "帖子内容",
    "content_type": 2,
    "images": [文件对象1, 文件对象2],
    "mentions": [2, 3] // @的用户ID列表
}
```

### 4.3 获取帖子详情

**接口地址**: `GET /posts/{post_id}/`

**响应示例**:
```json
{
    "code": 200,
    "message": "success",
    "data": {
        "post": {
            "id": 1,
            "title": "Python学习路线",
            "content": "<p>本文详细介绍Python学习路线...</p>",
            "content_type": 2,
            "author": {
                "id": 1,
                "nickname": "Python爱好者",
                "avatar": "/static/avatars/user1.png",
                "level": 5
            },
            "tieba": {
                "id": 1,
                "name": "python",
                "avatar": "/static/tieba/python.png"
            },
            "view_count": 1000,
            "reply_count": 50,
            "like_count": 100,
            "collect_count": 30,
            "is_top": false,
            "is_essence": true,
            "created_at": "2023-01-01T10:00:00Z",
            "updated_at": "2023-01-01T10:00:00Z",
            "images": [
                {
                    "url": "/static/posts/1/image1.jpg",
                    "thumbnail": "/static/posts/1/image1_thumb.jpg"
                }
            ],
            "mentions": [
                {
                    "id": 2,
                    "nickname": "被@用户"
                }
            ],
            "user_interaction": {
                "is_liked": true,
                "is_collected": false,
                "can_edit": true,
                "can_delete": true
            }
        }
    }
}
```

### 4.4 点赞帖子

**接口地址**: `POST /posts/{post_id}/like/`

**请求头**:
```
Authorization: Bearer {token}
```

**响应示例**:
```json
{
    "code": 200,
    "message": "点赞成功",
    "data": {
        "like_count": 101,
        "is_liked": true
    }
}
```

## 5. 评论管理接口

### 5.1 获取评论列表

**接口地址**: `GET /posts/{post_id}/comments/`

**查询参数**:
- `page`: 页码
- `page_size`: 每页数量
- `sort`: 排序方式 (time, hot)

**响应示例**:
```json
{
    "code": 200,
    "message": "success",
    "data": {
        "comments": [
            {
                "id": 1,
                "content": "这个帖子很有用！",
                "author": {
                    "id": 2,
                    "nickname": "评论用户",
                    "avatar": "/static/avatars/user2.png",
                    "level": 3
                },
                "floor_number": 1,
                "like_count": 10,
                "created_at": "2023-01-01T11:00:00Z",
                "replies": [
                    {
                        "id": 2,
                        "content": "我也觉得很有用",
                        "author": {
                            "id": 3,
                            "nickname": "回复用户"
                        },
                        "parent_id": 1,
                        "floor_number": 2,
                        "like_count": 5,
                        "created_at": "2023-01-01T12:00:00Z"
                    }
                ]
            }
        ],
        "pagination": {
            "page": 1,
            "page_size": 20,
            "total": 50,
            "total_pages": 3
        }
    }
}
```

### 5.2 发表评论

**接口地址**: `POST /posts/{post_id}/comments/`

**请求头**:
```
Authorization: Bearer {token}
```

**请求参数**:
```json
{
    "content": "评论内容",
    "parent_id": 0 // 0表示直接评论帖子，否则为回复评论
}
```

## 6. 搜索功能接口

### 6.1 综合搜索

**接口地址**: `GET /search/`

**查询参数**:
- `q`: 搜索关键词
- `type`: 搜索类型 (all, posts, users, tieba)
- `page`: 页码
- `page_size`: 每页数量

**响应示例**:
```json
{
    "code": 200,
    "message": "success",
    "data": {
        "query": "Python",
        "type": "all",
        "results": {
            "posts": [
                {
                    "id": 1,
                    "title": "Python学习路线",
                    "preview_content": "Python学习路线介绍...",
                    "author": {
                        "id": 1,
                        "nickname": "Python爱好者"
                    },
                    "tieba": {
                        "id": 1,
                        "name": "python"
                    },
                    "created_at": "2023-01-01T10:00:00Z"
                }
            ],
            "users": [
                {
                    "id": 2,
                    "nickname": "Python大师",
                    "avatar": "/static/avatars/user2.png",
                    "signature": "Python爱好者"
                }
            ],
            "tieba": [
                {
                    "id": 1,
                    "name": "python",
                    "description": "Python编程语言讨论",
                    "member_count": 10000
                }
            ]
        },
        "suggestions": ["Python教程", "Python基础", "Python进阶"],
        "total": 150
    }
}
```

### 6.2 热门搜索

**接口地址**: `GET /search/hot/`

**响应示例**:
```json
{
    "code": 200,
    "message": "success",
    "data": {
        "hot_searches": [
            {
                "keyword": "Python",
                "search_count": 1000,
                "trend": "up" // up, down, stable
            },
            {
                "keyword": "Java",
                "search_count": 800,
                "trend": "stable"
            }
        ]
    }
}
```

## 7. 消息系统接口

### 7.1 获取消息列表

**接口地址**: `GET /messages/`

**查询参数**:
- `type`: 消息类型 (all, system, comment, mention, private)
- `page`: 页码
- `page_size`: 每页数量
- `is_read`: 是否已读 (0:未读, 1:已读)

**请求头**:
```
Authorization: Bearer {token}
```

**响应示例**:
```json
{
    "code": 200,
    "message": "success",
    "data": {
        "messages": [
            {
                "id": 1,
                "type": "comment",
                "title": "新的评论",
                "content": "用户A评论了你的帖子",
                "related_post": {
                    "id": 1,
                    "title": "Python学习路线"
                },
                "sender": {
                    "id": 2,
                    "nickname": "用户A",
                    "avatar": "/static/avatars/user2.png"
                },
                "is_read": false,
                "created_at": "2023-01-01T10:00:00Z"
            }
        ],
        "unread_count": 5
    }
}
```

### 7.2 标记消息已读

**接口地址**: `POST /messages/{message_id}/read/`

**请求头**:
```
Authorization: Bearer {token}
```

## 8. 文件上传接口

### 8.1 上传图片

**接口地址**: `POST /upload/image/`

**请求头**:
```
Authorization: Bearer {token}
Content-Type: multipart/form-data
```

**请求参数**:
- `image`: 图片文件
- `type`: 图片类型 (avatar, post, tieba)

**响应示例**:
```json
{
    "code": 200,
    "message": "上传成功",
    "data": {
        "url": "/static/uploads/2023/01/01/abc123.jpg",
        "thumbnail": "/static/uploads/2023/01/01/abc123_thumb.jpg",
        "size": 102400,
        "width": 800,
        "height": 600
    }
}
```

## 9. WebSocket接口

### 9.1 实时消息推送

**连接地址**: `ws://localhost:8000/ws/`

**认证参数**:
```
ws://localhost:8000/ws/?token={jwt_token}
```

**消息类型**:
- `new_comment`: 新评论通知
- `new_message`: 新私信通知
- `system_notification`: 系统通知
- `online_status`: 在线状态更新

**消息格式**:
```json
{
    "type": "new_comment",
    "data": {
        "post_id": 1,
        "comment_id": 1,
        "author": {
            "id": 2,
            "nickname": "评论用户"
        },
        "content": "评论内容",
        "created_at": "2023-01-01T10:00:00Z"
    }
}
```

## 10. 错误处理

### 10.1 错误响应格式
```json
{
    "code": 400,
    "message": "请求参数错误",
    "errors": {
        "username": ["该用户名已存在"],
        "email": ["邮箱格式不正确"]
    },
    "timestamp": "2023-12-01T10:00:00Z"
}
```

### 10.2 常见错误码
| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| 1001 | 用户名已存在 | 更换用户名 |
| 1002 | 邮箱已存在 | 更换邮箱或找回密码 |
| 1003 | 手机号已存在 | 更换手机号 |
| 1004 | 验证码错误 | 重新获取验证码 |
| 2001 | 贴吧名称已存在 | 更换贴吧名称 |
| 2002 | 无权限操作 | 检查用户权限 |
| 3001 | 帖子不存在 | 检查帖子ID |
| 3002 | 评论内容为空 | 填写评论内容 |

## 11. API使用示例

### 11.1 Python请求示例
```python
import requests

# 用户登录
login_data = {
    "username": "testuser",
    "password": "password123"
}
response = requests.post("http://localhost:8000/api/auth/login/", json=login_data)
result = response.json()

token = result["data"]["token"]

# 获取帖子列表
headers = {"Authorization": f"Bearer {token}"}
params = {"page": 1, "page_size": 20}
response = requests.get("http://localhost:8000/api/posts/", headers=headers, params=params)
posts = response.json()["data"]["posts"]
```

### 11.2 JavaScript请求示例
```javascript
// 用户登录
const loginData = {
    username: 'testuser',
    password: 'password123'
};

fetch('/api/auth/login/', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify(loginData)
})
.then(response => response.json())
.then(data => {
    const token = data.data.token;
    
    // 获取用户信息
    return fetch('/api/auth/profile/', {
        headers: {
            'Authorization': `Bearer ${token}`
        }
    });
})
.then(response => response.json())
.then(data => {
    console.log('用户信息:', data.data.user);
});
```

## 12. 接口测试

### 12.1 使用Postman测试
1. 导入Postman集合
2. 设置环境变量：`base_url`, `token`
3. 先调用登录接口获取token
4. 使用token测试其他接口

### 12.2 自动化测试脚本
```python
# pytest测试示例
import pytest
import requests

class TestTiebaAPI:
    
    def setup_method(self):
        self.base_url = "http://localhost:8000/api"
        self.session = requests.Session()
    
    def test_user_register(self):
        data = {
            "username": "testuser",
            "password": "password123",
            "email": "test@example.com"
        }
        response = self.session.post(f"{self.base_url}/auth/register/", json=data)
        assert response.status_code == 200
        assert response.json()["code"] == 200
```

本API文档详细描述了百度贴吧项目的所有接口，包括请求参数、响应格式、错误处理等。开发人员可以根据此文档进行前后端接口联调。