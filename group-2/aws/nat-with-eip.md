---
description: What happens when we attach the EIP to NAT Gateway
icon: aws
---

# NAT with EIP

NAT Gateway is used to provide the network address translation, when you want to re-direct any traffic to a particular address. This is most commonly used, when we want to provide the internet access to the VM in private subnet ( without any route to internet )

This is done with the NAT Gateway placed in the public subnet and the EIP is attached to it. Below is the configuration to implement this in <mark style="background-color:yellow;">Terraform</mark>

<pre><code>resource "aws_eip" "one" {
  domain = "vpc"
  tags   = merge({ "Name" : "${var.prefix}-eip" }, var.default_tags)
}

# NAT gateway for routing traffic from private subnet to Internet
resource "aws_nat_gateway" "main" {
  subnet_id     = aws_subnet.public.id
  allocation_id = aws_eip.one.id <a data-footnote-ref href="#user-content-fn-1"># Elastic IP association</a>
  tags          = merge({ "Name" = "${var.prefix}-nat" }, var.default_tags)
  depends_on    = [aws_internet_gateway.main, aws_eip.one]
}
</code></pre>

### How does this Setup works?

In the route table for the VM ( that is in private subnet ), there is a route to re-direct the traffic to NAT Gateway. This traffic is routed to a defined IP ( provided by EIP ) which is in public subnet. Thus, the route table for public subnet will come into action ( routing any traffic for `0.0.0.0/0` to Internet Gateway )

## TLDR

* Public Subnet Route Table: The traffic is now originating from the NAT Gateway within the public subnet. The public subnet's route table directs all traffic destined for `0.0.0.0/0` to the Internet Gateway (IGW) (Target: `aws_internet_gateway.main.id`).
* Traffic to Internet: The IGW sends the traffic out to the internet, using the NAT Gateway's EIP as the source.
* Return Traffic: When the response comes back, it is addressed to the NAT Gateway's EIP. The NAT Gateway remembers the original private IP and port mapping, <mark style="background-color:green;">translates the destination EIP back to the VM's private IP</mark>, and sends the response to the VM.

[^1]: Attach the Elastic IP Address to NAT Gateway
