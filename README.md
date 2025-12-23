# studious-succotash
## 个人记账系统 （ C 语言程序）

### *^▽^*（GO!）可实现功能：
#### 1.用户管理功能
- 支持用户注册/登陆
- 用户密码
- 用户名重复检测
#### 2.数据隔离
#### 3.记账功能
- 记录收入/支出
- 可以查看记录
- 有增删改查功能
- 有收支统计，消费类型统计

##### 头文件和常量定义
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_user 100
#define MAX_bill 1000
```
##### 数据结构定义
###### 用户结构体
```
typedef struct {
    char username[50];
    char password[50];
    int nowlogged;    
} User;
```
###### 账单结构体
```
typedef struct {
    int id;
    char date[11];
    char state[10];  // 金钱状态，即支入，支出 
    float amount;
    char category[20];
    char note[100];  // 备注
    char username[50]; 
} Bill;
```
###### 全局变量
```
User users[MAX_user];          // 用户数组，存储所有用户
Bill bills[MAX_bill];          // 账单数组
int usernumber = 0;            // 用户数
int billnumber = 0;            // 账单记录数
User *currentUser = NULL;      // 当前登录的用户指针
```
###### 函数声明
```
void Menu();
void Userlogin();
void UserRegister();
void UserMenu();
void viewBill();
void addbill();
void statistics();
void deleteBill();     
void modifyBill();     
void categoryStats();  
int findBillById(int id);
```
###### 主函数
```
int main(int argc, char *argv[]) {
    int choice;
    printf("个人记账(^///^)\n");
    do {
        Menu();
        printf("请选择操作\n");
        scanf("%d", &choice);
        getchar();
        
        switch(choice) {
            case 1:
                UserRegister();
                system("pause");
                system("cls");
                break;
            case 2:
                Userlogin();
                system("pause");
                system("cls");
                break;
            case 3:
                printf("再见＞︿＜\n");
                system("pause");
                system("cls");
                break;
            default:
                printf("错误，请输入有效数字￣へ￣\n");
                system("pause");
                system("cls");
                break;
        }
    } while(choice != 3);
    
    return 0;
}
```
##### 函数功能实现
###### 用户注册
```
void UserRegister() {
    char username[50];
    char password[50];
    
    printf("用户注册\n");
    
    // 检查用户数量是否已达上限
    if(usernumber >= MAX_user) {
        printf("用户数量已达上限，无法注册新用户\n");
        return;
    }
    
    printf("请输入用户名: ");
    fgets(username, sizeof(username), stdin);
    username[strcspn(username, "\n")] = '\0';
    
    if(strlen(username) == 0) {
        printf("错误，用户名不能为空(* ￣︿￣)\n");
        return;
    }
    
    // 检查用户名是否已存在
    int i;
    for(i = 0; i < usernumber; i++) {
        if(strcmp(users[i].username, username) == 0) {
            printf("该用户名已存在，请使用其他用户名\n");
            return;
        }
    }
    
    printf("请输入密码: ");
    fgets(password, sizeof(password), stdin);
    password[strcspn(password, "\n")] = '\0';
    
    if(strlen(password) == 0) {
        printf("错误，密码不能为空╰（‵□′）╯\n");
        return;
    }
    
    // 保存新用户信息
    strcpy(users[usernumber].username, username);
    strcpy(users[usernumber].password, password);
    users[usernumber].nowlogged = 0;
    usernumber++;
    
    printf("注册成功，用户名是 %s\n", username);
    printf("当前用户总数: %d\n", usernumber);
}
```
###### 用户登录
```
void Userlogin() {
    char username[50];
    char password[50];
    int found = 0;
    
    printf("用户登陆\n");
    printf("请输入用户名: ");
    fgets(username, sizeof(username), stdin);
    username[strcspn(username, "\n")] = '\0';
    
    printf("请输入密码: ");
    fgets(password, sizeof(password), stdin);
    password[strcspn(password, "\n")] = '\0';
    
    // 在用户数组中查找匹配的用户
    int i;
    for(i = 0; i < usernumber; i++) {
        if(strcmp(users[i].username, username) == 0 && 
           strcmp(users[i].password, password) == 0) {
            currentUser = &users[i];  // 指向找到的用户
            users[i].nowlogged = 1;   // 设置登录状态
            found = 1;
            printf("登陆成功, 欢迎 %s *^____^*\n", username);
            printf("当前用户: %s\n", currentUser->username);
            UserMenu();
            break;
        }
    }
    
    if(!found) {
        printf("登陆失败，密码或用户名错误(°°)～\n");
    }
}
```
###### 主菜单
```
void Menu() {
    printf("主菜单\n");
    printf("1.用户注册\n");
    printf("2.用户登陆\n");
    printf("3.退出\n");
}
```
###### 用户菜单
```
void UserMenu() {
    int select;
    do {
        printf("用户菜单选择？(′▽｀)\n");
        printf("1.添加账单\n");
        printf("2.查看所有帐单\n");
        printf("3.统计\n");
        printf("4.删除账单\n");     
        printf("5.修改账单\n");     
        printf("6.消费类型统计\n"); 
        printf("7.退出登陆\n");
        printf("请选择: ");
        scanf("%d", &select);
        getchar();
        
        switch(select) {
            case 1:
                addbill();
                system("pause");
                system("cls");
                break; 
            case 2:
                viewBill();
                system("pause");
                system("cls");
                break;
            case 3:
                statistics();
                system("pause");
                system("cls");
                break;
            case 4:
                deleteBill();
                system("pause");
                system("cls");
                break;
            case 5:
                modifyBill();
                system("pause");
                system("cls");
                break;
            case 6:
                categoryStats();
                system("pause");
                system("cls");
                break;
            case 7:
                if(currentUser != NULL) {
                    currentUser->nowlogged = 0;
                    currentUser = NULL;
                }
                printf("已退出登陆－O－\n");
                system("pause");
                system("cls");
                break;
            default:
                printf("输入错误QAQ\n");
                system("pause");
                system("cls");
        } 
    } while(select != 7);
}
```
###### 查找账单
```
int findBillById(int id) {
    int i;
    for(i = 0; i < billnumber; i++) {
        if(bills[i].id == id && strcmp(bills[i].username, currentUser->username) == 0) {
            return i;  // 找到账单
        }
    }
    return -1; 
}
```
###### 添加账单
```
void addbill() {
    if(currentUser == NULL) {
        printf("错误，请先登陆(╯°□°）╯︵ ┻\n");
        return; 
    }
    
    if(billnumber >= MAX_bill) {
        printf("账单数达到上限＞﹏＜\n");
        return;
    }
    
    Bill newbill;
    printf("添加新账单\n");
    newbill.id = billnumber + 1;
    
    printf("请输入日期（格式：YYYY-MM-DD）: ");
    fgets(newbill.date, sizeof(newbill.date), stdin);
    newbill.date[strcspn(newbill.date, "\n")] = '\0';
    
    int choicetype;
    printf("请选择类型: 1.收入 2.支出: ");
    scanf("%d", &choicetype);
    getchar();
    
    if(choicetype == 1) {
        strcpy(newbill.state, "收入");
    } else if(choicetype == 2) {
        strcpy(newbill.state, "支出");
    } else {
        printf("输入错误＞︿＜\n");
        return;
    }
    
    printf("请输入金额: ");
    scanf("%f", &newbill.amount);
    getchar();
    
    printf("请输入类别（如：餐饮，购物...）: ");
    fgets(newbill.category, sizeof(newbill.category), stdin);
    newbill.category[strcspn(newbill.category, "\n")] = '\0';
    
    printf("请输入备注: ");
    fgets(newbill.note, sizeof(newbill.note), stdin);
    newbill.note[strcspn(newbill.note, "\n")] = '\0';
    
    // 关联当前用户的用户名
    strcpy(newbill.username, currentUser->username);
    bills[billnumber] = newbill;
    billnumber++;
    
    printf("账单添加成功，账单ID: %d\n", newbill.id);
}
```
###### 查看账单
```
void viewBill() {
    if(currentUser == NULL) {
        printf("错误，请先登陆(╯°□°）╯︵ ┻\n");
        return;
    }
    
    if(billnumber == 0) {
        printf("暂无账单记录<(＿　＿)>\n");
        return;
    }
    
    printf("用户 %s 的账单记录:\n", currentUser->username);
    printf("ID\t日期\t\t类型\t金额\t类别\t备注\n");
    int found = 0;
    
    int i;
    for(i = 0; i < billnumber; i++) {
        if(strcmp(bills[i].username, currentUser->username) == 0) {
            printf("%d\t%s\t%s\t%.1f\t%s\t%s\n", 
                   bills[i].id, bills[i].date, bills[i].state, 
                   bills[i].amount, bills[i].category, bills[i].note);
            found = 1;
        }
    }
    
    if(!found) {
        printf("该用户暂无账单记录\n");
    }
}
```
###### 统计功能
```
void statistics() {
    if(currentUser == NULL) {
        printf("错误，请先登陆(╯°□°）╯︵ ┻\n");
        return;
    }
    
    float totalincome = 0.0;
    float totalexpense = 0.0;
    float balance = 0.0;
    
    printf("用户 %s 的统计结果:\n", currentUser->username);
    
    int i;
    for(i = 0; i < billnumber; i++) {
        if(strcmp(bills[i].username, currentUser->username) == 0) {
            if(strcmp(bills[i].state, "收入") == 0) {
                totalincome += bills[i].amount;
            } else if(strcmp(bills[i].state, "支出") == 0) {
                totalexpense += bills[i].amount;
            }
        }
    }
    
    balance = totalincome - totalexpense;
    printf("总收入: %.2f元\n", totalincome);
    printf("总支出: %.2f元\n", totalexpense);
    printf("当前余额: %.2f元\n", balance);
    
    if(balance > 0) {
        printf("GOOD（￣︶￣）↗\n");
    } else if(balance == 0) {
        printf("收支平衡\n");
    } else {
        printf("注意开销(￣o￣) . z Z\n");
    }
}
```
###### 删除账单
```
void deleteBill() {
    if(currentUser == NULL) {
        printf("错误，请先登陆(╯°□°）╯︵ ┻\n");
        return;
    }
    
    int billId;
    printf("请输入要删除的账单ID: ");
    scanf("%d", &billId);
    getchar();
    
    int index = findBillById(billId);
    if(index == -1) {
        printf("未找到该账单或账单不属于当前用户(⊙_⊙)？\n");
        return;
    }
    
    printf("找到账单：\n");
    printf("ID: %d, 日期: %s, 类型: %s, 金额: %.2f, 类别: %s, 备注: %s\n",
           bills[index].id, bills[index].date, bills[index].state,
           bills[index].amount, bills[index].category, bills[index].note);
    
    char confirm;
    printf("确认删除此账单？(y/n): ");
    scanf("%c", &confirm);
    getchar();
    
    if(confirm == 'y' || confirm == 'Y') {
        // 将后面的账单向前移动，覆盖要删除的账单
        int i;
        for(i = index; i < billnumber - 1; i++) {
            bills[i] = bills[i + 1];
        }
        billnumber--;
        printf("账单删除成功！\n");
    } else {
        printf("取消删除\n");
    }
}
```
###### 修改账单
```
void modifyBill() {
    if(currentUser == NULL) {
        printf("错误，请先登陆(╯°□°）╯︵ ┻\n");
        return;
    }
    
    int billId;
    printf("请输入要修改的账单ID: ");
    scanf("%d", &billId);
    getchar();
    
    int index = findBillById(billId);
    if(index == -1) {
        printf("未找到该账单或账单不属于当前用户(⊙_⊙)？\n");
        return;
    }
    
    printf("找到账单：\n");
    printf("ID: %d, 日期: %s, 类型: %s, 金额: %.2f, 类别: %s, 备注: %s\n",
           bills[index].id, bills[index].date, bills[index].state,
           bills[index].amount, bills[index].category, bills[index].note);
    
    printf("修改账单信息（直接按回车跳过不修改）\n");
    
    // 修改日期
    printf("请输入新日期（格式：YYYY-MM-DD）: ");
    char newDate[11];
    fgets(newDate, sizeof(newDate), stdin);
    newDate[strcspn(newDate, "\n")] = '\0';
    if(strlen(newDate) > 0) {
        strcpy(bills[index].date, newDate);
    }
    
    // 修改类型
    printf("是否修改类型？(y/n): ");
    char changeType;
    scanf("%c", &changeType);
    getchar();
    
    if(changeType == 'y' || changeType == 'Y') {
        printf("请选择类型: 1.收入 2.支出: ");
        int choicetype;
        scanf("%d", &choicetype);
        getchar();
        
        if(choicetype == 1) {
            strcpy(bills[index].state, "收入");
        } else if(choicetype == 2) {
            strcpy(bills[index].state, "支出");
        }
    }
    
    // 修改金额
    printf("请输入新金额: ");
    char amountStr[20];
    fgets(amountStr, sizeof(amountStr), stdin);
    amountStr[strcspn(amountStr, "\n")] = '\0';
    if(strlen(amountStr) > 0) {
        bills[index].amount = atof(amountStr);
    }
    
    // 修改类别
    printf("请输入新类别: ");
    char newCategory[20];
    fgets(newCategory, sizeof(newCategory), stdin);
    newCategory[strcspn(newCategory, "\n")] = '\0';
    if(strlen(newCategory) > 0) {
        strcpy(bills[index].category, newCategory);
    }
    
    // 修改备注
    printf("请输入新备注: ");
    char newNote[100];
    fgets(newNote, sizeof(newNote), stdin);
    newNote[strcspn(newNote, "\n")] = '\0';
    if(strlen(newNote) > 0) {
        strcpy(bills[index].note, newNote);
    }
    
    printf("账单修改成功！\n");
}
```
###### 消费类型统计
```
void categoryStats() {
    if(currentUser == NULL) {
        printf("错误，请先登陆(╯°□°）╯︵ ┻\n");
        return;
    }
    
    // 用于统计每种消费类型的次数和总金额
    struct CategoryInfo {
        char name[20];
        int count;
        float totalAmount;
        char type[10];  // 收入或支出
    } categories[100];
    
    int categoryCount = 0;
    
    // 遍历当前用户的所有账单
    int i;
    for(i = 0; i < billnumber; i++) {
        if(strcmp(bills[i].username, currentUser->username) == 0) {
            // 查找该消费类型是否已记录
            int found = 0;
            int j;
            for(j = 0; j < categoryCount; j++) {
                if(strcmp(categories[j].name, bills[i].category) == 0 &&
                   strcmp(categories[j].type, bills[i].state) == 0) {
                    categories[j].count++;
                    categories[j].totalAmount += bills[i].amount;
                    found = 1;
                    break;
                }
            }
            
            if(!found) {
                strcpy(categories[categoryCount].name, bills[i].category);
                strcpy(categories[categoryCount].type, bills[i].state);
                categories[categoryCount].count = 1;
                categories[categoryCount].totalAmount = bills[i].amount;
                categoryCount++;
            }
        }
    }
    
    if(categoryCount == 0) {
        printf("暂无消费类型记录<(＿　＿)>\n");
        return;
    }
    
    printf("消费类型统计结果:\n");
    printf("类型\t\t次数\t总金额\t收入/支出\n");
    printf("--------------------------------------------\n");
    
    printf("【收入类型】:\n");
    for(i = 0; i < categoryCount; i++) {
        if(strcmp(categories[i].type, "收入") == 0) {
            printf("%s\t\t%d\t%.2f\t%s\n", 
                   categories[i].name, categories[i].count, 
                   categories[i].totalAmount, categories[i].type);
        }
    }
    
    printf("\n【支出类型】:\n");
    for(i = 0; i < categoryCount; i++) {
        if(strcmp(categories[i].type, "支出") == 0) {
            printf("%s\t\t%d\t%.2f\t%s\n", 
                   categories[i].name, categories[i].count, 
                   categories[i].totalAmount, categories[i].type);
        }
    }
}
```
