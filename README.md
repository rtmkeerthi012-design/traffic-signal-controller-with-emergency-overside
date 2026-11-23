module vending_machine(
    input clk,
    input reset,
    input [1:0] coin,
    input select_A,
    input select_B,
    output reg dispense_A,
    output reg dispense_B,
    output reg [4:0] change
);

reg [4:0] balance;

parameter IDLE = 2'b00,
          CHECK = 2'b01,
          DISPENSE = 2'b10;

reg [1:0] state;

always @(posedge clk or posedge reset)
begin
    if(reset)
    begin
        state <= IDLE;
        balance <= 0;
        dispense_A <= 0;
        dispense_B <= 0;
        change <= 0;
    end

    else
    begin
        dispense_A <= 0;
        dispense_B <= 0;
        change <= 0;

        case(state)

        IDLE:
        begin
            if(coin == 2'b01)
                balance <= balance + 5;
            else if(coin == 2'b10)
                balance <= balance + 10;

            if(select_A || select_B)
                state <= CHECK;
        end

        CHECK:
        begin
            if(select_A && balance >= 10)
            begin
                dispense_A <= 1;
                change <= balance - 10;
                balance <= 0;
                state <= IDLE;
            end

            else if(select_B && balance >= 15)
            begin
                dispense_B <= 1;
                change <= balance - 15;
                balance <= 0;
                state <= IDLE;
            end

            else
                state <= IDLE;
        end

        default:
            state <= IDLE;

        endcase
    end
end

endmodule# traffic-signal-controller-with-emergency-overside