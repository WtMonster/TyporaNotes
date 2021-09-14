### 2.两数相加

```c++
#include<iostream>
#include<stdlib.h>
using namespace std;

int main()
{
    struct ListNode {
        int val;
        ListNode* next;
        ListNode() : val(0), next(nullptr) {}
        ListNode(int x) : val(x), next(nullptr) {}
        ListNode(int x, ListNode* next) : val(x), next(next) {}
    };
    
        class Solution {
        public:
            ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {

                ListNode* head=new ListNode();
                ListNode* rec = head;
                bool temp = 0;
                while (l1 != NULL || l2 != NULL)
                {
            
                    if (l1 == NULL)
                    {
                        if ((l2->val) + temp <= 10)
                        {
                            head->next =new ListNode((l2->val) + temp, NULL);
                            temp = 0;
                        }
                        else
                        {
                            head->next = new ListNode((l2->val) + temp-10, NULL);
                            temp = 1;
                        }
                        l2 = l2->next;
                        head = head->next;
                    }
                    else if (l2 == NULL)
                    {
                        if ((l1->val) + temp <= 10)
                        {
                            head->next = new ListNode((l1->val) + temp, NULL);
                            temp = 0;
                        }
                        else
                        {
                            head->next = new ListNode((l1->val) + temp - 10, NULL);
                            temp = 1;
                        }
                        l1 = l1->next;
                        head = head->next;
                    }
                    else
                    {
                        if ((l1->val) +(l2->val)+ temp <= 10)
                        {
                            head->next = new ListNode((l1->val) + (l2->val) + temp, NULL);
                            temp = 0;
                        }
                        else
                        {
                            head->next = new ListNode((l1->val) + (l2->val) + temp - 10, NULL);
                            temp = 1;
                        }
                        l1 = l1->next;
                        l2 = l2->next;
                        head = head->next;
                    }
                }
                return rec->next;
            }
    };
}
```

