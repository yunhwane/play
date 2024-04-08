# 요구사항

- 최소 한종류 이상 상품 주문
- 한 상품을 한개 이상 주문 가능
- 총 주문금액은 각 상품 구매의 합
- 각 상품 구매 가격 합은 상품 가격에 구매 개수를 곱한 금액
- 주문할 때 배송지 정보를 무조건 지정해야함
- 출고하면 배송지 정보를 변경할 수 없음
- 출고 전에 주문 취소 불가
- 고객이 결제를 완료하기 전까지 상품을 준비하지 않음

### 주문

요구사항에서 알 수 있듯이 주문 취소, 배송지 정보 변경, 출고 상태로 변경, 결제 완료에 대한 기능 처리를 구현해야한다.

```java

public class Order{
    public void changeShipped(){...}
    public void changeShippingInfo(){...}
    public void cancel(){...}
    public void completePayment(){...}
}

```
### 주문 항목

- 한 상품을 한개 이상 주문 가능
- 각 상품 구매 가격의 합은 상품 가격에 구매 개수를 곱한 값


```java

public class OrderLine {
       private Product product;
       private int price;
       private int quantity;
       private int amounts;

       public OrderLine(Product product, int price, int quantity){
         this.Product = product;
         this.price = price;
         this.quantity = quantity;
         this.amounts = calculateAmounts();
       }

       private int calculateAmounts(){
           return this.quantity * this.price;
       }
       ...
}
```

### 주문과 주문 항목의 관계 

- 한 상품을 한개 이상 주문 가능
- 각 상품 구매 가격의 합은 상품 가격에 구매 개수를 곱한 값

```java

public class Order{
    private List<OrderLine> orderLines;
    private Money totalAmounts;

    public Order(List<OrderLine> orderLines){
        setOrderLines(orderLines)
    }

    private void setOrderLines(List<OrderLines> orderLines){
         verifyAtLeastOneOrOneMoreOrderLines(orderLines);
         this.orderLines = orderLines;
         calculateTotalAmounts();
     }

    private void verifyAtLeastOneOrOneMoreOrderLines(List<OrderLine> orderLines){
         if(orderLines == null || orderLines.isEmpty()){
             throw new IlleagalArgmentException("no orderLines");
    }

    private Money calculateTotalAmounts(){
          int sum = orderLines.stream().mapToInt(x -> x.getAmounts()).sum();
          this.totalAmounts = new Money(sum);
    }

    ...
}
```

- 한개 이상의 상품 주문 해야하기 떄문에 verifyAtLeastOneOrOneMoreOrderLines 유효성 검사
- calculateTotalAmounts 메서드를 통한 총 주문 금액 계산


### 배송지 정보

```java
public class shippingInfo{
      private String receiverName;
      private String receiverPhoneNumber;
      private String shippingAddress1;
      private String shippingAddress2;
      private String shippingZipCode;
}
```

요구사항을 봤을 때, 주문할 때 배송지 정보를 반드시 지정해야 한다의 요구사항을 충족해야한다.
따라서 주문에서, 배송지 정보를 함께 초기화 해야함


```java

public class Order{
    private List<OrderLine> orderLines;
    private ShippingInfo shippingInfo;
    private Money totalAmounts;

    public Order(List<OrderLine> orderLines, ShippingInfo shippingInfo){
        setOrderLines(orderLines);
        setShippingInfo(shippingInfo);
    }

    private void setOrderLines(List<OrderLines> orderLines){
         verifyAtLeastOneOrOneMoreOrderLines(orderLines);
         this.orderLines = orderLines;
         calculateTotalAmounts();
     }

    private void verifyAtLeastOneOrOneMoreOrderLines(List<OrderLine> orderLines){
         if(orderLines == null || orderLines.isEmpty()){
             throw new IlleagalArgmentException("no orderLines");
    }

    private Money calculateTotalAmounts(){
          int sum = orderLines.stream().mapToInt(x -> x.getAmounts()).sum();
          this.totalAmounts = new Money(sum);
    }

    private void setShippingInfo(ShippingInfo shippingInfo){
          if(shippingInfo == null) {
               throw new IlleagalArgmentException("Not empty shippingInfo");
          }
          this.shippingInfo = shippingInfo;
    }
    ...
}
```

### 출고 상태가 되기 전, 후의 요구사항

- 고객이 결제를 완료하기 전까지 상품을 준비하지 않는다.

```java

public enum OrderStatus{
   PAYMENT_WATING, PREPARING, SHIPPED, DELIVERING, DELVERY_COMPLTED, CANCLED;
}

```


### 출고 상태와의 주문의 관계
```java
public class Order{
    private List<OrderLine> orderLines;
    private ShippingInfo shippingInfo;
    private Money totalAmounts;
    private OrderStatus state;
    
    public Order(List<OrderLine> orderLines, ShippingInfo shippingInfo){
        setOrderLines(orderLines);
        setShippingInfo(shippingInfo);
    }


    public void changeShippingInfo(ShippingInfo newShippingInfo){
        verifyNotYetShipped();
        setShippingInfo(newShippingInfo);
    }

    public void cancel(){
        verifyNotYetShipped();
        this.state = OrderStatus.CANCELED;
    }

    private void verifyNotYetShipped(){
        if(state != OrderStatus.PAYMENT_WAITING && state != OrderStatus.PREPARING){
             throw new IlleagalArgmentException("already shipped");
    }


    private void setOrderLines(List<OrderLines> orderLines){
         verifyAtLeastOneOrOneMoreOrderLines(orderLines);
         this.orderLines = orderLines;
         calculateTotalAmounts();
     }

    private void verifyAtLeastOneOrOneMoreOrderLines(List<OrderLine> orderLines){
         if(orderLines == null || orderLines.isEmpty()){
             throw new IlleagalArgmentException("no orderLines");
    }

    private Money calculateTotalAmounts(){
          int sum = orderLines.stream().mapToInt(x -> x.getAmounts()).sum();
          this.totalAmounts = new Money(sum);
    }

    private void setShippingInfo(ShippingInfo shippingInfo){
          if(shippingInfo == null) {
               throw new IlleagalArgmentException("Not empty shippingInfo");
          }
          this.shippingInfo = shippingInfo;
    }

    
    ...
}
```
