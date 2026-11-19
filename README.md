makeSection("▸ ACESSO")

local keySection = Instance.new("Frame")
keySection.Size = UDim2.new(1, 0, 0, 54)
keySection.BackgroundColor3 = Color3.fromRGB(22, 20, 30)
keySection.BorderSizePixel = 0
keySection.LayoutOrder = nextOrder(); keySection.Parent = scroll
Instance.new("UICorner", keySection).CornerRadius = UDim.new(0, 6)

-- só a caixa, sem label indicando nada
local keyBox = Instance.new("TextBox")
keyBox.Size = UDim2.new(1, -80, 0, 36)
keyBox.Position = UDim2.new(0, 10, 0, 9)
keyBox.BackgroundColor3 = Color3.fromRGB(35, 30, 45)
keyBox.BorderSizePixel = 0
keyBox.Text = ""
keyBox.PlaceholderText = "Digite aqui..."
keyBox.TextColor3 = Color3.new(1,1,1)
keyBox.PlaceholderColor3 = Color3.fromRGB(120, 120, 120)
keyBox.Font = Enum.Font.Gotham
keyBox.TextSize = 13
keyBox.ClearTextOnFocus = false
keyBox.Parent = keySection
Instance.new("UICorner", keyBox).CornerRadius = UDim.new(0, 5)

local submit = Instance.new("TextButton")
submit.Size = UDim2.new(0, 60, 0, 36)
submit.Position = UDim2.new(1, -70, 0, 9)
submit.BackgroundColor3 = Color3.fromRGB(130, 0, 220)
submit.Text = "OK"
submit.TextColor3 = Color3.new(1,1,1)
submit.Font = Enum.Font.GothamBold
submit.TextSize = 14
submit.Parent = keySection
Instance.new("UICorner", submit).CornerRadius = UDim.new(0, 5)

-- feedback de erro (fica oculto, só aparece se errar a key)
local statusLbl = Instance.new("TextLabel")
statusLbl.Size = UDim2.new(1, -20, 0, 14)
statusLbl.Position = UDim2.new(0, 10, 0, 48)
statusLbl.BackgroundTransparency = 1
statusLbl.Text = ""
statusLbl.TextColor3 = Color3.fromRGB(255, 80, 80)
statusLbl.Font = Enum.Font.Gotham
statusLbl.TextSize = 11
statusLbl.TextXAlignment = Enum.TextXAlignment.Left
statusLbl.Parent = keySection
