### Generate Payment Stubs
    
    protoc -I ./payment --go_out ./golang/payment --go_opt paths=source_relative --go-grpc_out ./golang/payment --go-grpc_opt paths=source_relative ./payment/payment.proto

### Generate Order Stubs

    protoc -I ./order --go_out ./golang/order --go_opt paths=source_relative --go-grpc_out ./golang/order --go-grpc_opt paths=source_relative ./order/order.proto

### Generate Shipping Stubs

    protoc -I ./shipping --go_out ./golang/shipping --go_opt paths=source_relative --go-grpc_out ./golang/shipping --go-grpc_opt paths=source_relative ./shipping/shipping.proto